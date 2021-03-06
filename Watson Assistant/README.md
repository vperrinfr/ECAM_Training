# How to build a chatbot using Watson
We will be creating a bot to take coffee orders.

## Creating an IBM Cloud Account
1. Go to this link and create an account: https://console.bluemix.net/registration/
2. Or, go to https://console.bluemix.net/ and login if you have an account already and login

## Provisioning a Watson Assistant instance
1. Once logged in, click on `Catalog` in the upper right corner of the screen
2. Search for `Assistant`
3. Under the `Watson` Category, click on `Assistant`
4. Scroll down and make sure `Lite` Plan is selected for the free plan
5. Click `Create`
6. Click on `Launch tool`

## Creating a workspace
1. Once in the tooling, click `Create` to add a new workspace
2. Name it something like `Coffee-bot` and select the desired language

## Building Intents
1. Click `Add intent`
2. Name the new intent `order-drink`
3. Add a description of what the intent will do. For this, let's use "User wants to order a drink."
4. Hit `Enter` to create the intent
5. Start adding a few exaxmples of how a user would order a drink (at least 5 examples are recommended). Let's use the following:
  - i would like to order a coffee please
  - I need some caffeine
  - order espresso
  - a cappuccino would be lovely
  - a latte please
6. Open the `Try it Out` panel by clicking on the speech bubble in the upper right corner. This allows you to test how your bot will respond
7. Wait for the bot to finish training, then type `can I order a coffee`. It should classify the intent as `#order-drink`. Even though you didn't train the intent on this exact sentence, Watson can still understand it.
8. Add a few more intents to make your bot more robust. Try creating the following intents and adding a few examples to each:
  - #see-menu (User wants to see what's on the menu)
  - #greetings (User greets the bot)
  - #thanks (User thanks the bot)
  
Here are the finished intents:
![finished intents](https://github.com/desmarchris/think-lab/blob/master/pictures/finished-intents.png)

## Building Entities
1. Click on the `Entities` tab at the top of the page
2. Click `Add entity` and add the name `drink`
3. Turn `Fuzzy Matching` on if you want Watson to understand misspellings
4. Add a value `coffee` with the synonym of `cafe`. 
5. Add some additional values that you allow your users to order and any synonyms, for example:
  - espresso
  - cappuccino
  - latte
  - tea
6. Exit the page, and click on `System entities` underneath the `Entities` tab
7. Turn on `@sys-number`

Here is how your finished entity `@drink` should look:
![finished entity](https://github.com/desmarchris/think-lab/blob/master/pictures/finished-entity.png)

## Building Dialog
1. Click on the `Dialog` tab at the top of the page
2. Click `Create`
3. Click on the `Welcome` node if you would like to change the intro message
4. Click `Add node`, and name it `Greetings`
5. Add your `#greetings` intent as the field for `If bot recognizes`
6. Fill in a response that says something like "Hi! How can I help you today?"
7. Create two more nodes for `#thanks` and `#see-menu` and add responses
8. Create another node and name it `Order Drink`
9. To the right of the name, click on `Customize`
10. Turn on `Slots` and hit `Apply`
11. Add the intent `#order-drink`to `If bot recognizes`
12. Under `Check for`, add the entity `@drink`
13. Under `If not present, ask` add a question like "What would you like to drink?"
14. Click `Add slot`, and add a condition and prompt for `@sys-number`: "How many cups of $drink would you like?" (Note: the syntax `$variable` is short hand for accessing Context variables. Context variables allow you to pass information between your application and Conversation.)
15. Add in the response, "Ok, I have $number $drink coming right up!"

Finished dialog tree with `Order Drink` open:
![finished dialog](https://github.com/desmarchris/think-lab/blob/master/pictures/finished-dialog.png)

## If you want more...
Did you finish the above and want to learn more? Try some of the following methods to bolster your CoffeeBot.

### Resetting context
If your user orders a drink and completes the flow, and they try to make another order, the values found from the first flow will still be there so they will not be able to order something else. To fix this, we need to clear the context after a successful order so the values are not stored for the next order.
1. Create a node above the Slots node `Order Drink` called `Order Drink - Clear Context`
2. Set the condition to `#order-drink`
3. In the response section, click on the three button menu on the right and click on `Open context editor`
4. Fill in both of the variables (`drink` and `number`) and set the values to `null`
5. Click on the three dot menu on the right side of original Slots node `Order Drink`, and select `Move`. Then, click the new context clearing node and move to `As Child Node` (So, the parent node is the context clearing node, and the slots node is the child)
6. Go to the section called `And finally` at the bottom of the context clearing node. Select `Jump to` and click the slots node, then `If bot recognizes condition`
7. Change the condition of the slots node from `#order-drink` to `true` (Use this condition if you want the node to always fire)
8. Try it out! Without clearing the try it out panel, order a drink. Once finished, try ordering another drink and it should prompt you for the two needed variables again. Here's what the finished context clearing node will look like:
![clear context](https://github.com/desmarchris/think-lab/blob/master/pictures/clear-context.png)

### Help - Digressions
Sometimes, you will want an intent to be handled no matter where the user is in their flow. Think of Digressions as a global 'manage handlers': they allow you to respond to an intent even if a user is in the middle of a process flow, and then it allows them to return to their prior flow. If your user wants some help talking to the bot anywhere in your bot, this is a good intent to have digressions enabled.
1. Create a `#help` intent with examples like: "I need help"
2. Create a node below your `Order Drink` node
3. Add the condition of `#help` with a response like: "I can help you order a drink from my coffee shop. Just say order drink to get started!"
4. Go into the `Customize` portion of the node by clicking in the upper right
5. Click on the `Digressions` tab
6. Enable `Return after digression` (Digressions should be on by default, this setting allows you to handle the intent and then return back to the flow)
7. Now to test this out, we need to get in the middle of our order drink flow. But first, since it is a slot, we need to go into the `Digressions` tab in the `Order Drink` slots node
8. Turn on `Allow digressions away while slot filling` and click the button that only allows nodes with returns enabled. This will help you to control which nodes you want to allow to digress to
9. Try it out by saying "order drink", then when asked for what kind of drink you want, say "help". You should see a response from your help node with another follow up message for the next slot filling question
