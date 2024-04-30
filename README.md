# LLM Eval Examples

Collection of LLM eval examples using [evals orb](https://circleci.com/developer/orbs/orb/circleci/evals) on CircleCI.

## Prerequisites

Before runnning any of the examples, you'll need:

- A **CircleCI account** connected to your code. You can [sign up for free](https://circleci.com/signup/).
- An **OpenAI account**. Sign up for an OpenAI account at [openai.com](https://openai.com) to access their platform and API. Once logged into your OpenAI account, genreate your API key. Make note of the `API Key` and `Organization ID`.

Depending on your choice of evaluation provider, you will also need one of the following:
- A **[Braintrust](https://www.braintrustdata.com/) account**. Sign up for a Braintrust account at [www.braintrust.com](https://www.braintrust.com) to access their platform and API. Once logged into your Braintrust account, generate an `API Key` and make note of it.
- A **[LangSmith](https://smith.langchain.com/) account**. Sign up for a LangSmith account at [langsmith.com](https://langsmith.com) to use their language models API. Once logged into your LangSmith account, go to the API Keys page in your account settings to generate an API key. Copy this key to authenticate when using the LangSmith API.

The API keys will allow you to authenticate and interact with the APIs of your LLMOps tools to leverage their services. See their documentation for more details on capabilities and usage.

### Entering credentials into CircleCI

Entering your OpenAI, Braintrust and LangSmith credentials into CircleCI is easy. Navigate to `Project Settings` > `LLMOps`, and fill out the form by Clicking `Setup Integration`. This will create a context with environment variables with the credentials you've set up above. Make a note of the generated context name.

![LLMOps Integration Context](images/LLMOps-Integration-Context.png)

## The CirlceCI LLM-evaluations Orb

The LLM-evaluations Orb simplifies the definition and execution of evaluation jobs using popular third-party tools, and generates reports of evaluation results. 

Given the volatile nature of evaluations, evaluations orchestrated by the CircleCI LLM-evaluations orb do not halt the pipeline if an evaluation fails. This approach ensures that the inherent flakiness of evaluations does not disrupt the development cycle. 

Instead, a summary of the evaluation results can _optionally_ be presented :
- as a comment on the corresponding GitHub pull request
- as an artifact within the CircleCI User Interface

### Orb Parameters

The [evals orb](https://github.com/circleci-public/evals-orb) accepts the following parameters:

_Some of the parameters are optional based on the eval platform being used._

##### Common parameters

- `circle_pipeline_id` - CircleCI Pipeline ID

- `cmd` - Command to run the evaluation

- `eval_platform` - Evaluation platform (e.g. `braintrust`, `langsmith` etc. - default: `braintrust`)

- `evals_result_location` - Location to save evaluation results (default: `./results`)

##### Braintrust specific parameters

- `braintrust_experiment_name` (optional) - Braintrust experiment name. We will generate a unique name based on an md5 of "`<CIRCLE_PIPELINE_ID>_<CIRCLE_WORKFLOW_ID>`" if no `braintrust_experiment_name` is provided.

##### LangSmith specific parameters

- `langsmith_endpoint` - (optional) LangSmith API endpoint (default: `https://api.smith.langchain.com`)

- `langsmith_experiment_name` (optional) - LangSmith experiment name. We will generate a unique name based on an MD5 of "`<CIRCLE_PIPELINE_ID>_<CIRCLE_WORKFLOW_ID>`" if no `langsmith_experiment_name` is provided.  

## Getting started

Fork this repo to run evaluations on a LLM-based application using the [evals orb](https://circleci.com/developer/orbs/orb/circleci/evals). 
This repository includes evaluations that can be run on two evaluation platforms: [Braintrust](https://www.braintrustdata.com/) and [LangSmith](https://smith.langchain.com/). Each example folder contains instructions and sample code to run evaluations.

At a high level, when the workflow runs, our prompt is sent to OpenAI. We then get the results, and those are then fed to the respective eval platform. CircleCI will then store the results as an Artifact which you can go view. As well, if you've setup a Github Token CircleCI will post the eval result as a comment on your pull request.

The `.circleci/run_evals_config.yml` file uses the [evals orb](https://circleci.com/developer/orbs/orb/circleci/evals) to define jobs that run the evaluation code in each example folder. The orb handles setting up the evaluation environment, executing the evaluations, and collecting the results.

For example, the braintrust job runs the Python script in `braintrust/eval_tutorial.py` by passing it as the `cmd` parameter. It saves the evaluation results to the location specified with `evals_result_location`.

Similarly, the langsmith job runs the Python script in `langsmith/eval.py`.

To change where the results of the evaluation are being saved, go to the `evals/eval` step, and add the parameter `evals_result_location`: (Note: the eval orb will make the directory if it does not exist).

```yaml
- evals/eval:
    circle_pipeline_id: << pipeline.id >>
    eval_platform: ...
    evals_result_location: "./my-results-here"
    cmd: ...
```

To post a summary of the results as a comment on the PR:

- Generate a Github Personal Access Token with repo scope.
- Add this token as the environment variable `GITHUB_TOKEN` in the CircleCI project settings. Alternatively, you can also include this secret in the context created when you setup the LLM Ops integration. This will post a summary of the evaluation results as a PR comment.

The examples included in this repository use [dynamic configuration](https://circleci.com/docs/dynamic-config/) to selectively run only the evaluations defined in the folder that changed. So, for changes committed to the folder `braintrust`, only your Braintrust evaluations will be run; for changes committed to the folder `langsmith`, only your LangSmith evaluations will be run. 

```shell
.
├── README.md
├── braintrust
│   ├── eval_tutorial.py
│   ├── README.md 
│   └── requirements.txt
└── langsmith
    ├── dataset.py
    ├── eval.py
    ├── README.md
    └── requirements.txt
```

Happy Evaluating! Let us know if you have any feedback trying these out. Just submit an [issue](https://github.com/CircleCI-Public/llm-eval-examples/issues) on GitHub, or reach out to us at [ai-feedback@circleci.com](mailto:ai-feedback@circleci.com).
