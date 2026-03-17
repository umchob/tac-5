# Feature: Random Natural Language Query Generator

## Feature Description
Add a new button that generates random natural language queries based on existing database tables and their structure. The generated query will populate the input field, overwriting any existing content, allowing users to execute interesting queries manually. This feature enhances user experience by providing query inspiration and reducing the barrier to entry for new users who may not know what questions to ask of their data.

## User Story
As a user exploring my data
I want a button that generates interesting natural language queries based on my database structure
So that I can discover insights and understand what questions I can ask of my data without having to craft queries from scratch

## Problem Statement
Users often struggle with knowing what questions to ask of their data, especially when:
- They are new to the dataset and unfamiliar with available tables and columns
- They want to explore the data but lack inspiration for what queries to run
- They want to test the system capabilities without manually typing queries

Current manual query input requires users to both understand their data structure and formulate appropriate natural language questions, creating friction in the exploration process.

## Solution Statement
Implement a "Generate Random Query" button that:
1. Uses the existing `llm_processor.py` to generate contextual natural language queries
2. Reads the current database schema to understand available tables and columns
3. Generates interesting, relevant queries limited to two sentences maximum
4. Populates the query input field (always overwriting existing content)
5. Uses the secondary-button styling (same as "Upload Data" button)
6. Is placed apart from primary buttons to avoid confusion

This leverages existing LLM infrastructure to provide intelligent query suggestions based on actual data structure, ensuring queries are relevant and executable.

## Relevant Files
Use these files to implement the feature:

### Backend Files
- `app/server/core/llm_processor.py` - Contains existing LLM integration functions for OpenAI and Anthropic APIs. We will add a new function `generate_random_nl_query()` that generates natural language queries instead of SQL, using the existing schema formatting and API key management.

- `app/server/core/data_models.py` - Contains Pydantic models for API requests/responses. We will add:
  - `RandomQueryRequest` model (empty, no input needed)
  - `RandomQueryResponse` model with `query` field

- `app/server/server.py` - FastAPI application with existing endpoints. We will add a new `GET /api/random-query` endpoint that:
  - Retrieves the current database schema
  - Calls the new `generate_random_nl_query()` function
  - Returns the generated query

### Frontend Files
- `app/client/index.html` - Main HTML structure. We will add a new "Generate Random Query" button in the query-controls section, positioned apart from existing primary buttons (Query and Upload Data).

- `app/client/src/main.ts` - Main client-side logic. We will add:
  - New function `initializeRandomQueryButton()` to handle button click
  - Event listener to call the new API endpoint
  - Logic to populate the query input field with the generated query (overwriting existing content)
  - Integration with existing error handling

- `app/client/src/api/client.ts` - API client configuration. We will add:
  - New method `getRandomQuery()` to call the `/api/random-query` endpoint
  - Type definitions for `RandomQueryResponse`

- `app/client/src/types.d.ts` - TypeScript type definitions. We will add:
  - `RandomQueryRequest` interface (empty)
  - `RandomQueryResponse` interface with `query: string` field

### Style Files
- `app/client/src/style.css` - Application styles. We will add styles to position the random query button apart from existing buttons using CSS flexbox layout with margin-left: auto or a separate container.

### Test Files
- `.claude/commands/e2e/test_basic_query.md` - Reference for understanding E2E test structure
- `.claude/commands/test_e2e.md` - Reference for E2E test runner format

### New Files
- `.claude/commands/e2e/test_random_query_generator.md` - New E2E test file to validate the random query generator feature

## Implementation Plan

### Phase 1: Foundation
1. Add new Pydantic data models for the random query API
2. Create the backend LLM function to generate natural language queries
3. Add the FastAPI endpoint for random query generation

### Phase 2: Core Implementation
1. Add TypeScript type definitions for the new API
2. Implement the API client method to call the new endpoint
3. Add the "Generate Random Query" button to the HTML
4. Implement the client-side button click handler

### Phase 3: Integration
1. Style the button to match the "Upload Data" button style (secondary-button)
2. Position the button apart from primary buttons
3. Test the complete flow from button click to query generation
4. Ensure error handling works correctly

## Step by Step Tasks
IMPORTANT: Execute every step in order, top to bottom.

### 1. Create E2E Test File
Create a new E2E test file to validate the random query generator feature:
- Read `.claude/commands/test_e2e.md` to understand the E2E test format
- Read `.claude/commands/e2e/test_basic_query.md` as a reference
- Create `.claude/commands/e2e/test_random_query_generator.md` with test steps to:
  - Navigate to the application
  - Verify the "Generate Random Query" button exists
  - Click the button
  - Verify a query appears in the input field
  - Verify the query is different from empty
  - Take screenshots at key steps
  - Verify the button styling matches "Upload Data" button
  - Verify button positioning is apart from primary buttons

### 2. Add Backend Data Models
- Read `app/server/core/data_models.py`
- Add `RandomQueryRequest` class (empty Pydantic BaseModel)
- Add `RandomQueryResponse` class with `query: str` and optional `error: str` fields
- Follow existing Pydantic model patterns

### 3. Implement Random Query Generation Function
- Read `app/server/core/llm_processor.py` to understand existing LLM integration
- Add new function `generate_random_nl_query(schema_info: Dict[str, Any]) -> str`
- Use existing `format_schema_for_prompt()` function to format schema
- Create a prompt that instructs the LLM to:
  - Generate an interesting natural language query based on the schema
  - Limit the query to two sentences maximum
  - Make the query relevant to the available tables and columns
  - Return ONLY the query text, no explanations
- Use existing OpenAI/Anthropic API key management logic (prioritize OpenAI, fallback to Anthropic)
- Return the generated query string
- Handle errors appropriately

### 4. Add Backend API Endpoint
- Read `app/server/server.py` to understand existing endpoint patterns
- Add new `GET /api/random-query` endpoint
- Import the new `generate_random_nl_query` function and data models
- In the endpoint:
  - Get the current database schema using `get_database_schema()`
  - Call `generate_random_nl_query(schema_info)` to generate the query
  - Return `RandomQueryResponse` with the generated query
  - Handle errors and return error response if generation fails
- Add proper logging following existing patterns

### 5. Add Frontend TypeScript Types
- Read `app/client/src/types.d.ts`
- Add `RandomQueryRequest` interface (can be empty object `{} `)
- Add `RandomQueryResponse` interface with `query: string` and optional `error?: string` fields
- Follow existing TypeScript type patterns

### 6. Add Frontend API Client Method
- Read `app/client/src/api/client.ts`
- Add new `getRandomQuery()` method to the `api` object
- Use the existing `apiRequest<T>()` helper function
- Call `GET /api/random-query` endpoint
- Return type should be `RandomQueryResponse`
- Follow existing API method patterns

### 7. Add Generate Random Query Button to HTML
- Read `app/client/index.html`
- In the `query-controls` section, add a new button:
  - ID: `generate-random-query-button`
  - Text: "Generate Random Query"
  - Class: `secondary-button` (same as Upload Data button)
- Position it apart from existing primary buttons using a wrapper div or CSS
- Ensure it's visually distinct from the Query button

### 8. Implement Random Query Button Handler
- Read `app/client/src/main.ts`
- Add new function `initializeRandomQueryButton()`
- In the function:
  - Get reference to the generate random query button element
  - Add click event listener
  - On click:
    - Disable the button and show loading state
    - Call `api.getRandomQuery()`
    - On success:
      - Get reference to query input element
      - Set `queryInput.value = response.query` (this overwrites existing content)
      - Re-enable the button
    - On error:
      - Display error message using existing `displayError()` function
      - Re-enable the button
- Call `initializeRandomQueryButton()` in the `DOMContentLoaded` event listener

### 9. Style the Random Query Button
- Read `app/client/src/style.css`
- Add styles to position the "Generate Random Query" button apart from primary buttons
- Use existing `.secondary-button` styles (white background, primary color border)
- Consider adding a new CSS class or using flexbox with `margin-left: auto` to position it separately
- Ensure button has proper spacing and hover effects

### 10. Testing and Validation
- Test the complete flow: button click → API call → query generation → input population
- Verify the query is always overwritten (not appended)
- Verify the query is limited to two sentences
- Verify error handling works when no tables exist
- Verify the button styling matches "Upload Data" button
- Verify button positioning is apart from primary buttons

### 11. Run Validation Commands
Execute all validation commands to ensure zero regressions:
- Run backend tests
- Run frontend TypeScript checks
- Run frontend build
- Run E2E tests (including the new random query generator test)

## Testing Strategy

### Unit Tests
- Test `generate_random_nl_query()` function with various schema structures
- Test the API endpoint `/api/random-query` with:
  - Valid schemas (multiple tables)
  - Empty schemas (no tables)
  - Error scenarios (API key failures)
- Test frontend `getRandomQuery()` method

### Edge Cases
- No tables in database (should return helpful error message)
- LLM API failures (should handle gracefully and show error)
- Multiple button clicks in quick succession (should disable button during request)
- Empty query response from LLM (should handle gracefully)
- Query longer than expected (should truncate or show error)

### Integration Tests
- Complete flow from button click to query generation
- Query input field is properly overwritten
- Loading states display correctly
- Error messages display appropriately
- Generated queries are contextually relevant to the database schema

## Acceptance Criteria
- [ ] New "Generate Random Query" button exists in the UI
- [ ] Button uses secondary-button styling (same as "Upload Data" button)
- [ ] Button is positioned apart from primary buttons (Query and Upload Data)
- [ ] Clicking the button calls the backend API endpoint
- [ ] Backend generates a natural language query based on database schema
- [ ] Generated query is limited to two sentences maximum
- [ ] Query is populated in the input field (overwriting any existing content)
- [ ] Button shows loading state during generation
- [ ] Errors are displayed appropriately if generation fails
- [ ] When no tables exist, a helpful error message is shown
- [ ] Feature works with both OpenAI and Anthropic APIs
- [ ] All existing tests pass (zero regressions)
- [ ] E2E test validates the complete feature

## Validation Commands
Execute every command to validate the feature works correctly with zero regressions.

### Backend Tests
```bash
cd app/server && uv run pytest
```
Run server tests to validate the new endpoint and LLM function work correctly with zero regressions.

### Frontend TypeScript Checks
```bash
cd app/client && bun tsc --noEmit
```
Run frontend TypeScript compiler to validate no type errors were introduced.

### Frontend Build
```bash
cd app/client && bun run build
```
Run frontend build to ensure the application builds successfully with the new feature.

### E2E Test
Read `.claude/commands/test_e2e.md`, then read and execute the new E2E test file `.claude/commands/e2e/test_random_query_generator.md` to validate this functionality works:
```bash
# Read the test runner instructions
cat .claude/commands/test_e2e.md

# Execute the E2E test (follow the test runner instructions)
```

### Manual Testing Steps
1. Start the server and client using `./scripts/start.sh`
2. Navigate to http://localhost:5173
3. Upload sample data (Users, Products, or Events)
4. Click "Generate Random Query" button
5. Verify a query appears in the input field
6. Verify clicking the button again overwrites the previous query
7. Run the generated query to verify it works
8. Test error scenarios (no tables, API failures)

## Notes

### LLM Prompt Design
The prompt for generating random queries should:
- Be concise but descriptive
- Encourage diverse query types (filtering, aggregation, joining)
- Reference specific table and column names from the schema
- Enforce the two-sentence limit
- Instruct the LLM to return ONLY the query text

Example prompt structure:
```
Given the following database schema:
[formatted schema]

Generate ONE interesting natural language query that a user might ask about this data.
Requirements:
- Maximum 2 sentences
- Be specific and reference actual table/column names
- Make it different each time
- Return ONLY the query text, no explanations

Query:
```

### API Key Management
- Uses existing environment variables (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`)
- Prioritizes OpenAI if available, falls back to Anthropic
- No new API keys or configuration needed

### Future Considerations
- Could add query history to avoid repeating recent queries
- Could add difficulty levels (simple vs complex queries)
- Could add query category selection (filtering, aggregation, joins)
- Could cache queries for performance
- Could add user feedback loop to improve query suggestions

### Security Considerations
- The generated query is natural language, not SQL, so SQL injection is not a concern
- Input validation still applies when the user executes the generated query
- No new security vulnerabilities introduced

### Performance Considerations
- LLM API calls may take 1-3 seconds
- Button should show loading state during generation
- Consider adding rate limiting if abused
- No database performance impact (reads only schema)

### Dependencies
- No new Python packages needed (uses existing `openai` and `anthropic` packages)
- No new frontend dependencies needed
