Welcome to SonarQube workshop. In this exercise we will look at core SonarQube features.

<details>
  <summary>Task 1. Initial setup</summary>
Use this repository template to create a new repository. If the Action gods were merciful today, the automation has already invited you to SonarQube Workshop organisation, created a repository from the template and used your GutHub username for repository name.

If you haven't done so yet, please log into SonarQube Cloud from https://sonarcloud.io/login. Please make sure to use GitHub for authentication. 

![GitHub Login](workshop_images/github_login.jpg)

While it's possible to log in with other DevOps platforms, we will be using GitHub in this exercise. Your SonarQube Cloud account will be created if this is the first time you are logging into the platform. Once your account is created, you automatically will be added to the SonarQube Workshop organisation.
</details>

<details>
  <summary>Task 2. Create a SonarQube Cloud project from a GitHub Repository</summary>

To create a new project in SonarQube Cloud from your GitHub repository, follow these steps:
  1. Log in to [SonarQube Cloud](https://sonarcloud.io/login) using your GitHub account.
  2. Click on **"+ Analyze new project"** 
  ![Create project](workshop_images/create_project.jpg)
  
  3. Make sure to select **SonarQube Workshop** in organisation list. In the list of repositories, find and select the repository that was created for you. Make sure the repository name includes your GitHub username (e.g., `sq-workshop-yourusername`). Click on **Set Up** button.
  ![Select the repository](workshop_images/select_reporitory.jpg)
  
  4. In the **Set up project for Clean as You Code** screen, select **Number of days** and accept the default 30 days period. Click on **Create project** button.
  ![New code](workshop_images/clean_as_you_code.jpg)
  
  5. SonarQube will start the analysis of the project which will take a few minutes
  ![Initial analysis](workshop_images/initial_analysis.jpg)
  
  6. When the initial analysis is completed, you should be able to see all issues found by SonarQube (make sure to select `Main branch` on the left):
  ![Initial analysis result](workshop_images/initial_analysis_result.jpg)
  </details>

<details>
  <summary>Task 3. Review the results</summary>
  
  1. Go to `Issues` tab. Here you can see all the issues that were detected in your code. Feel free to filter by various parameters. 

  2. Go to `Security Hotspots` tab. A security hotspot highlights a security-sensitive piece of code that the developer needs to review. SonarQube Cloud helps you find security hotspots in your code when running analyses. You can read more about Security Hotspots on https://docs.sonarsource.com/sonarqube-cloud/digging-deeper/security-hotspots
  
  3. Go to `Inventory` -> `Dependencies`. This is where Sonar reports on what [third party libraries](https://docs.sonarsource.com/sonarqube-cloud/advanced-security/viewing-dependencies) were imported into your application. But wait.... why there are 0 dependnencies? If you look at `package.json` file in your repositories - there are definitely a few packages that were declared! 

  The reason for this is how the scanning is configured. With GitHub it is possible to have your code [scanned automatically](https://docs.sonarsource.com/sonarqube-cloud/advanced-setup/automatic-analysis). In order to scan for vulnerable packages we need to implement scanning in our pipelines.
</details>

<details>
  <summary>Task 4. Setup Sonar scanning in GitHub Actions</summary>
  
  1. Go to `Administration` -> `Analisys Method`. 
  
  ![Analysis Method](workshop_images/analysis_method.jpg)

  As you can see, the automated analysis is enabled by default. We will need to turn that off and set up the analysis with GitHub Actions. Disable the automatic analysis and click on `With GitHub Actions`:
  
  ![Setup analysis](workshop_images/setup_analysis.jpg)

  Follow these steps to setup the scanning in GitHub Actions:
  
  2. Create `SONAR_TOKEN` secret in your test repository in GitHub:

  ![Create new secret](workshop_images/new_repository_secret.jpg)

  ![Create SONAR_TOKEN](workshop_images/sonar_token.jpg)

  3. Create a new workflow in `.github/workflows` directory in your test repository in GitHub. Click on `JS/TS & Web` to get the code for the workflow:

  ![Workflow details](workshop_images/workflow_details.jpg)

  ![Add new file](workshop_images/create_new_file.jpg)

  ![Create the workflow](workshop_images/create_workflow.jpg)

  ![Commit the workflow](workshop_images/commit_workflow.jpg)

  4. Create `sonar-project.properties` file in root directory in your test repository in GitHub:

  ![sonar-project.properties file](workshop_images/sonar_project_properties.jpg)

  Creation of `sonar-project.properties` will trigger the workflow which you will be able to monitor in Actions tab:

  ![Actions](workshop_images/actions.jpg)

  ![Sonar workflow run](workshop_images/sonar_workflow_run.jpg)

  5. Once the workflow has finished, you should be able to see the list of dependencies in `Inventory` -> `Dependencies` and list of vulnerable dependencies in `Dependency Risks` tab:

  ![Dependencies](workshop_images/dependencies.jpg)

  ![Dependency Risks](workshop_images/dependency_risks.jpg)
  
</details>

<details>
  <summary>Task 5. Introduce a vulnerable dependency</summary>
  
  1. Open package.json file for editing and change line #175 from `"sanitize-html": "2.12.1"` to `"sanitize-html": "1.4.2"`
  
  ![packages.json file](workshop_images/packages_json.jpg)
  ![Edit packages.json file](workshop_images/edit_packages_json.jpg)
  
  2. Commit the changes to a brand new branch and create a pull request
  
  ![Commit packages.json file](workshop_images/commit_packages_json.jpg)
  ![New branch](workshop_images/new_branch_packages_json.jpg)
  ![New pull request](workshop_images/new_pr_packages_json.jpg)
  ![Create pull request](workshop_images/create_pr_packages_json.jpg)
  
  3. Wait until the scan is completed and review the messages in the pull request
  
  ![Quality gate fail](workshop_images/cg_fail_packages_json.jpg)

</details>
