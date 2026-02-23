#afhultin
GitHub Actions Setup

The config file is in the github/workflows/ folder.

How to enable:
1. Copy the github/ folder into the root of the DevOpps repo and rename it to .github (needs the dot in front for GitHub to pick it up)
2. Commit and push to main
3. Done, CI runs automatically on every push and pull request

What it does:
Every push to main or pull request against main triggers a build that compiles the project (mvn -DskipTests package), runs the tests (mvn test), and builds the Docker image (docker build). Results show up as a green check or red X on the pull request page.

Optional: enforce CI before merging:
1. Go to repo Settings, then Branches, then Add branch protection rule
2. Branch name pattern: main
3. Check "Require status checks to pass before merging"
4. Select build-and-test
5. Save
