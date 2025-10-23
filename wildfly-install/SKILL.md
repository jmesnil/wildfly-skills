---
name: WildFly Installation and configuration
description: >
  Install, provision and configure WildFly to be able to deploy a Entreprise Java application (.war, .ear or .jar files).
dependencies: java>=17
---
# WildFly Installation Skill

You are a WildFly provisioning expert. Your task is to provision, configure, deploy, and start a WildFly server based on a provided deployment file (that can be a .war, .ear or .jar)

For any modification to the WildFly configuration, create `.cli` files and execute them with the `jboss-cli.sh` command.

## Objective

Help users provision an optimized WildFly server that automatically detects the required features from a .war file, optionally configure layers, deploy the application, and start the server.

## Prerequisites

- Verify that WildFly Glow tool is available
  - if not, download it with: `curl -s https://jmesnil.github.io/wildfly.org/sh/install-glow | sh`
- Ensure the deployment file exists at the provided path

## Workflow

### Step 1: Gather Information

Ask the user for the following information:

1. **deployment file path**: Absolute path to the deployment file
   - Validate that the file exists
   - If not provided, look for .war files in the current directory or in the target/ subdirectory

2. **Target directory**: Where to provision the WildFly server
   - Default: `./server` in the current directory
   - Must be a non-existing directory (parent must exist)
   - Allow user to specify a different directory

3. **Server version**:
   - Default: Latest WildFly version
   - Allow user to specify a specific version

4. **Layers & Add-ons**: (Optional)
   - First, use wildfly-glow to scan the deployment by running `wildfly-glow scan -s {deploymentPath}`
   - Present the list to the user and ask which ones they want to enable
   - Common add-ons: `wildfly-cli`, `openapi`, `health`, `metrics`

5. **Datasource configuration**: (Optional)
   - Ask if they need to configure datasources beyond what's auto-detected
   - If yes, gather:
     - Datasource name
     - JNDI name
     - Database type (H2, PostgreSQL, MySQL, etc.)
     - Connection URL
     - Username/password


### Step 2: Provision the Server

Use WildFly Glow with:
- `deploymentPath`: The absolute path to the .war file
- `targetDirectory`: The target directory for the server
- `serverVersion`: If specified by user
- `addOns`: Array of selected add-ons
- `spaces`: If user wants to enable incubating features

This tool uses WildFly Glow to automatically detect required features from the deployment and provisions an optimized server.

### Step 3: Create an admin user (Optional)        

Ask the user if they want to create a user or admun user with the `bin/add-user.sh` (if this file is installed in the target directory).

### Step 4: Start the Server

Start the provisioned WildFly server:
1. Navigate to the target directory
2. Start the server using: `./bin/standalone.sh` (or `bin\standalone.bat` on Windows)
3. Use the Bash tool with `run_in_background: true` so the user can continue working

### Step 5: Verify Deployment

1. Wait a few seconds for the server to start
3. Verify that the `standalone.log` in the server has a log with WLFYSRV0025
3. Display the deployment URL to the user (typically `http://localhost:8080/{context-root}`)

### Step 6: Provide Usage Information

Inform the user:
1. Server location: `{targetDirectory}`
2. Application URL: `http://localhost:8080/{context-root}`
5. Management interface (if enabled): `http://localhost:9990`

### Step 7: Reproduce the execution (optional)

Propose to the user to create a `code.md` file to capture
all the executions that were performed to complete his task to install WildFly.

## Error Handling

- If provisioning fails, check the error message and help the user resolve issues
- Check if WildFly Glow is propertly installed
- If the .war file doesn't exist, help locate it
- If the target directory already exists, ask if they want to use a different path or remove the existing one
- If server fails to start, check its logs

## Best Practices

1. Always use absolute paths for file operations
3. Provide clear, actionable feedback to the user
4. If anything fails, provide specific error messages and suggested fixes
5. Use the TodoWrite tool to track progress through the workflow
6. Keep the user informed at each step

## Example Interaction

## Notes

- WildFly Glow automatically detects required features (JAX-RS, CDI, JPA, etc.)
- The provisioned server is optimized and only includes necessary subsystems
- All configuration is done offline (embed-server) before starting for reliability

