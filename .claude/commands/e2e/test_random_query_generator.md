# E2E Test: Random Query Generator

Test the Random Natural Language Query Generator feature.

## User Story

As a user exploring my data
I want a button that generates interesting natural language queries based on my database structure
So that I can discover insights and understand what questions I can ask of my data without having to craft queries from scratch

## Test Steps

1. Navigate to the `Application URL`
2. **Verify** the page title is "Natural Language SQL Interface"
3. **Verify** the "Generate Random Query" button exists
4. **Verify** the button uses secondary-button styling (same as "Upload Data" button)
5. **Verify** the button is positioned apart from primary buttons (Query and Upload Data)
6. Take a screenshot of the initial state
7. Upload sample data (Users Data) to ensure tables exist
8. Wait for the table to load successfully
9. Take a screenshot of the table loaded state
10. Click the "Generate Random Query" button
11. **Verify** the button shows loading state during generation
12. Wait for the query to be generated
13. **Verify** a query appears in the input field
14. **Verify** the query is different from empty
15. **Verify** the query is limited to two sentences (approximately)
16. Take a screenshot of the generated query
17. Click the "Generate Random Query" button again
18. **Verify** the previous query is overwritten (not appended)
19. Take a screenshot of the second generated query
20. **Verify** the second query is different from the first query

## Success Criteria

- "Generate Random Query" button exists in the UI
- Button uses secondary-button styling (white background, primary color border)
- Button is positioned apart from primary buttons
- Button shows loading state during generation
- Query is populated in the input field (overwriting any existing content)
- Generated query is different from empty
- Generated query is limited to two sentences
- Each click generates a different query (queries are not identical)
- 4 screenshots are taken
