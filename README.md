# ODM RPA Decision Bot Tutorial

This tutorial describes step-by-step how to create an IBM ODM Decision Bot for IBM RPA with Automation Anywhere from
an existing Decision Service.

## Table of Contents

  - [Summary](#summary)
  - [Prerequisites](#prerequisites)
  - [Deploy the Decision Form](#deploy-the-decision-form)
  - [Create the Decision Bot](#create-the-decision-bot)
  - [Invoke the Decision Bot from an RPA task.](#invoke-the-decision-bot-from-an-rpa-task)

---

## Summary

In the demo [odm-rpa-invoicing-sample](https://github.com/ODMDev/odm-rpa-invoicing-sample), we show how
to delegate the decision logic of an RPA task to an IBM ODM Decision Bot, using the [ODM decision forms generator](https://www.npmjs.com/package/odm-decision-forms).

In this tutorial, we show how to create that **Decision Bot** for **IBM RPA with Automation Anywhere** from scratch
and step-by-step.

We first show how to deploy a [Decision Form](https://github.com/ODMDev/odm-decision-forms) on top of an ODM Decision Service.

Then, we demonstrate how to create an RPA *MetaBot* to encapsulate the invocation of the Decision Form.

Finally, we show how the *MetaBot* can be easily called from an RPA task by the RPA author without requiring any 
knowledge of the underlying implementation.

## Prerequisites

You must have already deployed a Decision Service that you want to use in your RPA tasks to IBM ODM Decision Server.

As an example, you may deploy the Invoicing sample decision service provided in the [odm-rpa-invoicing-sample](https://github.com/ODMDev/odm-rpa-invoicing-sample) demo.

We assume that your ODM servers are running at [http://localhost:9080](http://localhost:9080). If this is not the case,
you must adapt the options of the [ODM Decision Form](https://github.com/ODMDev/odm-decision-forms) server, as described in
the Usage section.

You must also have NodeJS installed.

## Deploy the Decision Form

Install the odm-decision-forms package:

```bash
npm install -g odm-decision-forms
```

Run the Decision Form server.

```bash
odm-decision-forms
```

* Open your browser at [http://localhost:3000](http://localhost:3000) to see a list of the available forms.
* Select the decision form you want to automate. Eg: http://localhost:3000/ruleapp/CompleteInvoice_Ruleapp/CompleteInvoice_Ruleset

<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Decision Form.png?v=2" width="75%"></img>

## Create the Decision Bot

* Keep the Decision Form window open and close any other tab or browser instance.
* Open *Automation Anywhere Client*
* Go to the *MetaBot* tab and click *New*
* Set a name for the MetaBot. Eg: Invoicing Decision Bot
* In the Applications list, Select *Internet Explorer* and click *Create*

<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/New MetaBot.png" width="30%"></img>

* This opens the MetaBot editor. Click 'Add Screen' and select the screen corresponding to the navigator displaying your Decision Form

<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Add Screen.png" width="50%"></img>

* Click on the *Logic* tab
* Click *Add Logic*. This opens the *Logic Editor*, showing the Internet Explorer window

<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Logic Editor.png" width="75%"></img>

* On the right hand side, expand the *Variable Manager* panel
* Create an input variable corresponding to each input field of the decision form (*Request* panel):
   * Click 'Add'
   * Give a name to the variable, similar to the field name
   * Select 'Input' as the *Parameter Type*
   * Enter a default Value in the value field, to be used for testing
   * Repeat for each field in the 'Request' panel
   
<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Add Input Variable.png" width="75%"></img>

* Create an output variable corresponding for each output field of the decision form (*Response* panel):
   * Click 'Add'
   * Give a name to the variable, similar to the field name
   * Select 'Output' as the *Parameter Type*
   * Enter a default Value in the value field, to be used for testing
   * Repeat for each field in the 'Response' panel

* In our example, you will end up with 6 variables:

<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/MetaBot Variables.png" width="25%"></img>

Once the variables are created, you must map each input / output field of the Decision Service to a corresponding variable.

* Map a Select (combo box):
   * Click the Select. This will highlight it in red
   * Set the *Select Action* field to *SelectItemByText*
   * Set the *Select Item* field to the variable you want to map (you can hit F2 to see the list of available variables)
   * Click *Add*

   <img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Map Select In.png" width="60%"></img>

* Map a Text field (input type="text"):
   * Click in the text field. This will highlight it in red
   * Set the *Select Action* field to *SetText*
   * Check the *Keystrokes* checkbox - this is mandatory for odm-decision-forms
   * Set the 'Enter Text' field to the variable you want to map (you can hit F2 to see the list of available variables)
   * Click *Add*

   <img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Map TextField In.png" width="60%"></img>

You can then ask the MetaBot to execute the decision

* Click the *Run Decision* button. This will highlight it in red
* Set the 'Select Action' field to *Click*
* Click *Add*
   
<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Run Decision.png" width="55%"></img>

You must map each output field of the Decision Service to a corresponding output variable.

* Map a Select (combo box):
   * Click the Select. This will highlight it in red
   * Set the *Select Action* field to *GetSelectedText*
   * Set the *Select Variable* field to the variable you want to map (you can hit F2 to see the list of available variables) 
   * Click *Add*

   <img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Map Select Out.png" width="60%"></img>

* Map a Text field (input type="text"):
   * Click in the text field. This will highlight it in red
   * Set the *Select Action* field to *Get Property*
   * Set the *Property Name* field to *HTML Value*
   * Set the *Select Variable* field to the variable you want to map (you can hit F2 to see the list of available variables) 
   * Click *Add*

   <img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Map TextField Out.png" width="75%"></img>

* Once variables are all created and mapped to input / output fields, you should see an 'Action List' similar to this:

   <img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Action List.png" width="100%"></img>

* Save your logic

## Invoke the Decision Bot from an RPA task.

Now that the ODM Decision Service execution is encapsulated in a MetaBot, it is very easy to call that MetaBot from an RPA task.

From your Main task, you first need to open the browser to the correct URL: drag and drop the *Web Recorder / Open Browser*
command in your task, and enter the Decision Form URL.

<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Open Browser.png" width="75%"></img>
   
Now, drag and drop your Decision Bot from the *MetaBots* list, and map the variables of your main task to the ones of your metabot.

<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Invoke MetaBot.png" width="75%"></img>

Your main task should look like this:

<img src="https://raw.githubusercontent.com/ODMDev/odm-rpa-decisionbot-tutorial/master/screenshots/Main Bot.png" width="100%"></img>


# Issues and contributions
For issues relating to this sample, please use the [GitHub issue tracker](../../issues).
We welcome contributions following [our guidelines](CONTRIBUTING.md).

# License
The source files found in this project are licensed under the [Apache License 2.0](LICENSE).

# Notice
© Copyright IBM Corporation 2017.

