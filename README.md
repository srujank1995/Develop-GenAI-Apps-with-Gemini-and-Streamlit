# Chef App with Vertex AI and Streamlit

This repository demonstrates the process of creating and deploying a Chef app using Vertex AI, Streamlit, Docker, and Google Cloud Run. The app provides meal recommendations based on user input, including dietary preferences, allergies, available ingredients, and wine preferences.

## Table of Contents
- [Overview](#overview)
- [Task 1: Test API Connectivity](#task-1-test-api-connectivity)
- [Task 2: Update Streamlit Application](#task-2-update-streamlit-application)
- [Task 3: Test the Application](#task-3-test-the-application)
- [Task 4: Modify Dockerfile and Push Image](#task-4-modify-dockerfile-and-push-image)
- [Task 5: Deploy to Cloud Run](#task-5-deploy-to-cloud-run)

## Overview
This repository contains the necessary files and instructions to test, update, and deploy a Chef application that integrates with Google Cloud's Vertex AI for generating recipe recommendations based on user inputs.

## Task 1: Test API Connectivity

    1. **Download the `prompt.ipynb` file:**

       Open a terminal in Vertex AI Workbench and execute:
       ```bash
       gsutil cp gs://spls/gsp517/prompt.ipynb .
       ```

    2. **Modify `prompt.ipynb`:**

       - Edit cell 3 to include your `project_ID` and `region`.
       - Replace the existing prompt in cell 5 with the following:
         ```text
         I am a Chef. I need to create Japanese recipes for customers who want low sodium meals. However, I do not want to include recipes that use ingredients associated with a peanuts food allergy. I have ahi tuna, fresh ginger, and edamame in my kitchen and other ingredients. The customer wine preference is red. Please provide some for meal recommendations. For each recommendation include preparation instructions, time to prepare and the recipe title at the beginning of the response. Then include the wine pairing for each recommendation. At the end of the recommendation provide the calories associated with the meal and the nutritional facts.
         ```

    3. **Run all cells and observe the results.**
    
    4. **Save `prompt.ipynb`.**

## Task 2: Update Streamlit Application

    1. **Clone the GitHub Repository:**
       ```bash
       git clone https://github.com/GoogleCloudPlatform/generative-ai.git
       ```
    
    2. **Navigate to the directory:**
       ```bash
       cd generative-ai/gemini/sample-apps/gemini-streamlit-cloudrun
       ```
    
    3. **Download the `chef.py` file:**
       ```bash
       gsutil cp gs://spls/gsp517/chef.py .
       ```

    4. **Update `chef.py`:**
       - Add a radio button for the `wine` variable with options: Red, White, None.
       - Add the following Gemini prompt in Python code:
         ```python
         prompt = f"""I am a Chef. I need to create {cuisine} recipes for customers who want {dietary_preference} meals. However, don't include recipes that use ingredients with the customer's {allergy} allergy. I have {ingredient_1}, {ingredient_2}, and {ingredient_3} in my kitchen and other ingredients. The customer's wine preference is {wine}. Please provide some meal recommendations. For each recommendation include preparation instructions, time to prepare, and the recipe title at the beginning of the response. Then include the wine pairing for each recommendation. At the end of the recommendation provide the calories associated with the meal and the nutritional facts."""
         ```

    5. **Upload `chef.py` to the bucket:**
       ```bash
       gcloud storage cp chef.py gs://set at lab start-generative-ai/
       ```

## Task 3: Test the Application

    1. **Set up the Python virtual environment and install dependencies:**
    
       Ensure you are in the `generative-ai/gemini/sample-apps/gemini-streamlit-cloudrun` directory. Set the environment variables for `PROJECT` and `REGION`.
    
    2. **Run the `chef.py` application:**
       ```bash
       streamlit run chef.py
       ```
    
    3. **Test the application in Cloud Shell and confirm it is working as expected.**

## Task 4: Modify Dockerfile and Push Image

    1. **Modify the Dockerfile:**
       Update the Dockerfile to use the `chef.py` file. Save the changes.
    
    2. **Set environment variables:**
       ```bash
       export AR_REPO='chef-repo'
       export SERVICE_NAME='chef-streamlit-app'
       ```

    3. **Create the Artifact Registry repository and submit the build:**
       ```bash
       gcloud artifacts repositories create $AR_REPO --location=$REGION --repository-format=docker
       gcloud builds submit --tag "$REGION-docker.pkg.dev/$PROJECT/$AR_REPO/$SERVICE_NAME"
       ```
    
    4. **Wait for the command to complete.**

## Task 5: Deploy to Cloud Run

    1. **Deploy the application:**
       ```bash
       gcloud run deploy $SERVICE_NAME \
         --image "$REGION-docker.pkg.dev/$PROJECT/$AR_REPO/$SERVICE_NAME" \
         --port 8080 \
         --allow-unauthenticated \
         --region $REGION \
         --platform managed \
         --project $PROJECT \
         --set-env-vars PROJECT=$PROJECT,REGION=$REGION
   ```

    2. **Visit the provided URL and test the deployed application.**

## Conclusion

This repository demonstrates a complete workflow from testing API connectivity, updating a Streamlit application, modifying Docker configurations, and deploying to Cloud Run. Each step is crucial for successfully creating and deploying a functional Chef app with Vertex AI and Streamlit.

