---
lab:
    title: 'Implement Retrieval Augmented Generation (RAG) with Azure OpenAI Service'
---

# Implement Retrieval Augmented Generation (RAG) with Azure OpenAI Service

The Azure OpenAI Service enables you to use your own data with the intelligence of the underlying LLM. You can limit the model to only use your data for pertinent topics, or blend it with results from the pre-trained model.

In the scenario for this exercise, you will perform the role of a software developer working for Margie's Travel Agency. You will explore how to use generative AI to make searching tasks easier and more efficient. The techniques used in the exercise can be applied on any content.

This exercise will take approximately **20** minutes.

## Provision an Azure OpenAI resource

An AzureOpenAI Model is already provisioned for you. Please use the endpoint and API key shared by the instructor for this lab. 

## Observe normal chat behavior without adding your own data

Before connecting Azure OpenAI to your data, let's first observe how the base model responds to queries without any grounding data.

1. Use your knowledge from the previous lab to create a new system prompt and interact with the model about traveling. Ask the model 
    ```prompt
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```prompt
    What are some facts about New York?
    ```

    Try similar questions about tourism and places to stay for other locations that will be included in our grounding data, such as London, or San Francisco. You'll likely get complete responses about areas or neighborhoods, and some general facts about the city.

## Connect your data in the chat playground

Now you'll add some data for a fictional travel agent company named *Margie's Travel*. Then you'll see how the Azure OpenAI model responds when using the brochures from Margie's Travel as grounding data.

First analyze the pdf files provided  **Labfiles/02-use-own-data/data**, which contain information about various destinations offered by the company *Margie's Travel*.

Next, let's explore the use of your own data in an app that uses the Azure OpenAI service SDK. You'll develop your app using Visual Studio Code. The code files for your app have been provided in a GitHub repo.

1. Start Visual Studio Code.
2. Open the palette (SHIFT+CTRL+P) and run a **Git: Clone** command to clone the `https://github.com/khalilchouchen1994/tumai-demo` repository to a local folder (it doesn't matter which folder).
3. When the repository has been cloned, open the folder in Visual Studio Code.

    > **Note**: If Visual Studio Code shows you a pop-up message to prompt you to trust the code you are opening, click on **Yes, I trust the authors** option in the pop-up.

4. Wait while additional files are installed to support the C# code projects in the repo.

    > **Note**: If you are prompted to add required assets to build and debug, select **Not Now**.

## Configure your application

Applications for both C# and Python have been provided, and both apps feature the same functionality. First, you'll complete some key parts of the application to enable using your Azure OpenAI resource.

1. In Visual Studio Code, in the **Explorer** pane, browse to the **Labfiles/02-use-own-data** folder and expand the **CSharp** or **Python** folder depending on your language preference. Each folder contains the language-specific files for an app into which you're going to integrate Azure OpenAI functionality.
2. Right-click the **CSharp** or **Python** folder containing your code files and open an integrated terminal. Then install the Azure OpenAI SDK package by running the appropriate command for your language preference:

    **C#**:

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python**:

    ```
    pip install openai==1.13.3
    pip install python-dotenv
    ```

3. In the **Explorer** pane, in the **CSharp** or **Python** folder, open the configuration file for your preferred language

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Update the configuration values with the input provided by the intructor to include:
    - The  **endpoint** and a **key** from the Azure OpenAI resource shared by the instructor 
    - The **deployment name** for your model deployment 
    - The endpoint for your search service 
    - A **key** for your search resource
    - The name of the search index (which should be `margiestravel`).
1. Save the configuration file.

### Add code to use the Azure OpenAI service

Now you're ready to use the Azure OpenAI SDK to consume your deployed model.

1. In the **Explorer** pane, in the **CSharp** or **Python** folder, open the code file for your preferred language, and replace the comment ***Configure your data source*** with code to add the Azure OpenAI SDK library:

    **C#**: ownData.cs

    ```csharp
    // Configure your data source
    AzureSearchChatExtensionConfiguration ownDataConfig = new()
    {
            SearchEndpoint = new Uri(azureSearchEndpoint),
            Authentication = new OnYourDataApiKeyAuthenticationOptions(azureSearchKey),
            IndexName = azureSearchIndex
    };
    ```

    **Python**: ownData.py

    ```python
    # Configure your data source
    extension_config = dict(dataSources = [  
            { 
                "type": "AzureCognitiveSearch", 
                "parameters": { 
                    "endpoint":azure_search_endpoint, 
                    "key": azure_search_key, 
                    "indexName": azure_search_index,
                }
            }]
        )
    ```

2. Review the rest of the code, noting the use of the *extensions* in the request body that is used to provide information about the data source settings.

3. Save the changes to the code file.

## Run your application

Now that your app has been configured, run it to send your request to your model and observe the response. You'll notice the only difference between the different options is the content of the prompt, all other parameters (such as token count and temperature) remain the same for each request.

1. In the interactive terminal pane, ensure the folder context is the folder for your preferred language. Then enter the following command to run the application.

    - **C#**: `dotnet run`
    - **Python**: `python ownData.py`

    > **Tip**: You can use the **Maximize panel size** (**^**) icon in the terminal toolbar to see more of the console text.

2. ask the same questions as you did previously, and see how the response differs with some details of the data used to ground the prompt, which was obtained from your search service..

```prompt
I'd like to take a trip to New York. Where should I stay?
```

```prompt
What are some facts about New York?
``` 