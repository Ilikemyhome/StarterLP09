# Learning Practice: Enhancing Testing with Large Language Models

## Topics

- Large language models in software testing
- Human and AI collaboration
- Prompt context and specificity
- AI-assisted test design
- Evaluating AI-generated test ideas
- Hallucinations and unsupported assumptions

## Learning Objectives

- Identify ways an LLM can assist with software testing.
- Compare the results of vague and detailed prompts.
- Provide relevant requirements and context in a prompt.
- Evaluate AI-generated tests for accuracy and usefulness.
- Identify missing requirements, vague results, and unsupported assumptions.
- Use human judgment to improve AI-generated test ideas.

## The Product

You will use an LLM to help design tests for North Vacation Booking.

The application has the following requirements:

- A reservation must be between 1 and 31 nights.
- A reservation must include between 1 and 12 guests.
- The price is $200 per night.
- Each valid reservation includes a one-time $50 cleaning fee.
- Each guest over five costs an additional $25 per night.
- The check-in date must occur before the checkout date.
- The checkout date is not counted as a reserved night.
- All requested nights must be available.
- Invalid booking information produces an error.
- Unavailable dates produce an unavailable error.

You are responsible for reviewing and verifying everything produced by the LLM.

## Step 1: Create Your Own Test Ideas

|       Requirement                             |           Input or Action         |       Expected Result       |
| ----------------------------------------------| --------------------------------- | --------------------------- |
|Reservation must be between 1 and 31 nights	| Create reservation for 0 nights	| Error message displayed     |
|Reservation must be between 1 and 31 nights    | Create reservation for 31 nights	| Reservation accepted        |
|Reservation must include between 1 and 12 guests |Create reservation with 13 guests| Error message displayed     |
|Each valid reservation includes a $50 cleaning fee| Reserve 2 nights for 2 guests  |Total = $450 ($200 × 2 + $50)|
|Check-in date must occur before checkout date  |Set check-in and checkout on same date| Error message displayed  |


## Step 2: Use a General Prompt


| AI-Generated Idea                 | Useful, Vague, Unsupported, or Incorrect |        Explanation                 |
| --------------------------------- | ---------------------------------------- | ---------------------------------- |
| Search available properties       |           Reject	                       | Properties and search functionality are not listed in requirements |
| Calculate taxes	                |           Reject                         | No tax requirement provided        |
| Promo code validation         	|           Reject	                       | Promo codes not mentioned          |     
| Create confirmed reservation      |           Revise	                       | Reservation creation exists but booking references are unsupported |
| Double-booking prevention         |           Revise	                       | Availability is required but concurrency handling isn't specified |
| Payment failure handling	        |           Reject	                       | Payment system not specified       |
| Cancellation testing              |           Reject                         | Cancellation requirements not provided |
| Unauthorized user access          |           Reject                         | Authentication not specified       |
| Leap year boundary                |           Revise                         | Date calculations are relevant but leap year behavior is not required |
| Invalid date order                |           Accept                         | Directly supported by requirements  | 

## Step 3: Create a Contextual Prompt

Act as a software test analyst.

Generate 10 test ideas for the North Vacation Booking application.

Requirements:

- A reservation must be between 1 and 31 nights.
- A reservation must include between 1 and 12 guests.
- The price is $200 per night.
- Each valid reservation includes a one-time $50 cleaning fee.
- Each guest over five costs an additional $25 per night.
- The check-in date must occur before the checkout date.
- The checkout date is not counted as a reserved night.
- All requested nights must be available.
- Invalid booking information produces an error.
- Unavailable dates produce an unavailable error.

Use boundary value analysis and equivalence partitioning where appropriate.

For each test idea provide:
- Requirement being tested
- Test inputs
- Expected result
- Risk being addressed

Do not invent requirements that are not explicitly listed.

## Step 4: Evaluate the Contextual Response


|   AI-Generated Idea     | Accept, Revise, or Reject |         Reason              | Required Correction |
| ----------------------- | ------------------------- | --------------------------- | ------------------- |
| 0-night reservation     |       Accept              | Correct boundary value test |       None          |
| 1-night reservation     |       Accept              | Correct minimum valid boundary |    None          |
| 31-night reservation	  |       Accept	          | Correct maximum valid boundary |	None          |
| 32-night reservation	  |       Accept	          | Correct invalid boundary	|       None          | 
| 1 guest reservation	  |       Accept	          | Correct minimum guest count	|       None          |  
| 12 guest reservation	  |       Accept	          | Correct maximum guest count	|       None          |  
| 13 guest reservation	  |       Accept	          | Correct invalid guest count	|       None          |
| Extra guest fee calculation |   Accept	          | Correctly verifies surcharge rule| 	None          |
| Check-in equals checkout| 	  Accept	          | Valid date validation test  | 	    None          |  
| Unavailable dates       |       Accept              | Directly tests availability requirement| None     |  

## Step 5: Compare the Prompts

### Which prompt produced more useful test ideas?

The contextual prompt produced more useful test ideas because it included the application's requirements and instructed the LLM not to invent unsupported functionality.

### How did adding requirements change the response?

The response became more focused on reservation boundaries, guest limits, pricing calculations, and availability checks.

### One weakness in the general response?

The general response invented features such as promo codes, cancellations, taxes, payment processing, authentication, and property searches that were not included in the requirements.

### One weakness that remained in the contextual response?

The contextual response did not explicitly test the requirement that the checkout date is not counted as a reserved night.

### One test idea the LLM suggested that I had not considered?

Testing the additional guest fee calculation for more than five guests.

### The test idea I created that the LLM missed?

Verifying that a reservation from July 1 to July 2 is billed as exactly one night because the checkout date is not counted.

## Step 6: Create the Final Test Cases

| Test Case ID |                Requirement                      |    Preconditions    |        Inputs or Actions       | Expected Result | Source |
| ------------ | ----------------------------------------------- | ------------------- | ------------------------------ | --------------- | ------ |
| TC-01	       | Reservation must be between 1 and 31 nights     | Dates are available | Create reservation for 0 nights|	Error message displayed because reservation length is below minimum	| Human-generated |
| TC-02	       | Reservation must be between 1 and 31 nights     | Dates are available | Create reservation for 31 nights with 2 guests	| Reservation accepted. Total cost = $6,250 ($200 × 31 + $50 cleaning fee) | AI-generated and verified |
| TC-03	       | Reservation must include between 1 and 12 guests| Dates are available | Create reservation for 13 guests|	Error message displayed because guest count exceeds maximum	 | AI-generated and verified |
| TC-04	       | Each guest over five costs an additional $25 per night | Dates are available| Create reservation for 2 nights with 6 guests|	Reservation accepted. Total cost = $500 ($400 + $50 cleaning fee + $50 guest surcharge) | AI-generated and verified |
| TC-05	       | Check-in date must occur before checkout date   | Dates are available | Set check-in date equal to checkout date |	Error message displayed because check-in must occur before checkout	| Human-generated |
| TC-06	       | Checkout date is not counted as a reserved night| Dates are available | Check-in July 1, checkout July 2, 1 guest |	Reservation counted as exactly 1 night. Total cost = $250	| AI-generated and revised |
| TC-07	       | All requested nights must be available	Selected dates contain at least one unavailable night |	Attempt reservation	| Unavailable error message displayed	| AI-generated and verified |

## Step 7: Reflect on Human and AI Responsibilities

### 1. What did the LLM help you complete more quickly?

The LLM helped generate test ideas quickly and identify boundary value scenarios based on the requirements. It provided a starting point for designing test cases and helped organize the information into a structured format.

### 2. What information did the LLM misunderstand, overlook, or invent?

The initial response invented several features that were not included in the requirements, such as payment processing, refunds, taxes, promo codes, authentication, cancellation policies, and property searches. The contextual response was more accurate but initially overlooked explicitly testing the requirement that the checkout date is not counted as a reserved night.

### 3. What knowledge did you need to evaluate its response?

I needed knowledge of software testing techniques such as boundary value analysis and equivalence partitioning. I also needed to understand the application requirements in order to identify unsupported assumptions, verify price calculations, and ensure that expected results were correct.

### 4. What could happen if a tester used the response without reviewing it?

A tester could spend time creating or executing tests for functionality that does not exist. Incorrect or unsupported tests could waste resources, create confusion, and leave important requirements untested. Defects in the actual system might be missed.

### 5. Which testing decisions should remain the responsibility of the tester?

The tester should remain responsible for interpreting requirements, selecting appropriate test techniques, validating expected results, identifying risks, reviewing AI-generated suggestions, and deciding which tests should ultimately be included in the test suite. AI can assist with brainstorming, but the tester must verify that all tests are accurate and relevant.

## What to Submit

1. Create and initialize a GitHub repository.
2. Commit and push your work to the repository.
3. Make sure the repository is public. Private repositories will not receive credit.
4. Submit the URL on canvas

## AI Transparency

AI was used to help create and clean up this file.
