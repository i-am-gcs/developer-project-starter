# Project Start Checklist

Use this checklist before, during, and after building a JavaScript or Express project.

## Before coding

- [ ] Read the complete task carefully
- [ ] Identify the required data
- [ ] Decide whether a frontend is needed
- [ ] Decide whether a backend is needed
- [ ] List the required API endpoints
- [ ] Decide whether JSON files or a database are needed
- [ ] Plan the folder structure
- [ ] Identify the required user interactions
- [ ] Define the smallest useful first version
- [ ] Write down assumptions and open questions
- [ ] Define what “done” means

## Repository and configuration

- [ ] Initialize Git and create a focused `.gitignore`
- [ ] Write a short README with setup and run instructions
- [ ] Add useful npm scripts
- [ ] Keep secrets and machine-specific settings out of Git
- [ ] Provide a safe `.env.example` when configuration is required
- [ ] Make an initial commit before major implementation work

## Backend

- [ ] Initialize the Node.js project
- [ ] Install the required dependencies
- [ ] Create the Express server
- [ ] Configure middleware and static files
- [ ] Confirm that the server starts without errors
- [ ] Implement the first endpoint
- [ ] Test each API endpoint independently
- [ ] Return appropriate HTTP status codes
- [ ] Validate and normalize external input
- [ ] Handle missing resources and unexpected errors
- [ ] Keep route handlers small and move reusable logic into modules

## Frontend

- [ ] Create the HTML structure
- [ ] Connect the CSS and JavaScript files
- [ ] Fetch data from the backend
- [ ] Check the response before reading its body
- [ ] Convert the response to JSON
- [ ] Render the data in the page
- [ ] Add event listeners
- [ ] Add styling after the core functionality works
- [ ] Show loading, empty, success, and error states
- [ ] Check keyboard navigation and form labels
- [ ] Test the layout at narrow and wide viewport sizes

## Quality

- [ ] Format and lint the code
- [ ] Test the main success paths
- [ ] Test invalid input and failure paths
- [ ] Confirm that a clean clone can be installed and started
- [ ] Update documentation when commands or behavior change

## Final checks

- [ ] The server starts without errors
- [ ] API endpoints return the expected responses
- [ ] Invalid requests receive useful error responses
- [ ] Fetch requests work
- [ ] The UI renders correctly
- [ ] User interactions work
- [ ] The browser console contains no unexpected errors
- [ ] Unnecessary debug logs have been removed
- [ ] The complete application has been tested from start to finish
- [ ] No secrets, credentials, or local-only files are tracked
- [ ] README setup instructions work as written
