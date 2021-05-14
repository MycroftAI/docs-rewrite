---
description: >-
  A Skill can add context to the Intent Parser to create more natural
  interaction with Mycroft.
---

# Conversational Context

_NOTE: Conversational context is currently only available with the_ [_Adapt_](https://mycroft.ai/documentation/adapt) _Intent Parser, and is not yet available for_ [_Padatious_](https://mycroft.ai/documentation/padatious)

> How tall is John Cleese?

`"John Cleese is 196 centimeters"`

> Where's he from?

`"He's from England"`

Context is added manually by the **Skill** creator using either the `self.set_context()` method or the `@adds_context()` decorator.

Consider the following intent handlers:

```python
    @intent_handler(IntentBuilder().require('PythonPerson').require('Length'))
    def handle_length(self, message):
        python = message.data.get('PythonPerson')
        self.speak('{} is {} cm tall'.format(python, length_dict[python]))

    @intent_handler(IntentBuilder().require('PythonPerson').require('WhereFrom'))
    def handle_from(self, message):
        python = message.data.get('PythonPerson')
        self.speak('{} is from {}'.format(python, from_dict[python]))
```

To interact with the above handlers the user would need to say

```text
User: How tall is John Cleese?
Mycroft: John Cleese is 196 centimeters
User: Where is John Cleese from?
Mycroft: He's from England
```

To get a more natural response the functions can be changed to let Mycroft know which `PythonPerson` we're talking about by using the `self.set_context()` method to give context:

```python
    @intent_handler(IntentBuilder().require('PythonPerson').require('Length'))
    def handle_length(self, message):
        # PythonPerson can be any of the Monty Python members
        python = message.data.get('PythonPerson')
        self.speak('{} is {} cm tall'.format(python, length_dict[python]))
        self.set_context('PythonPerson', python)

    @intent_handler(IntentBuilder().require('PythonPerson').require('WhereFrom'))
    def handle_from(self, message):
        # PythonPerson can be any of the Monty Python members
        python = message.data.get('PythonPerson')
        self.speak('He is from {}'.format(from_dict[python]))
        self.set_context('PythonPerson', python)
```

When either of the methods are called the `PythonPerson` keyword is added to Mycroft's context, which means that if there is a match with `Length` but `PythonPerson` is missing Mycroft will assume the last mention of that keyword. The interaction can now become the one described at the top of the page.

> User: How tall is John Cleese?

Mycroft detects the `Length` keyword and the `PythonPerson` keyword

> Mycroft: 196 centimeters

John Cleese is added to the current context

> User: Where's he from?

Mycroft detects the `WhereFrom` keyword but not any `PythonPerson` keyword. The Context Manager is activated and returns the latest entry of `PythonPerson` which is _John Cleese_

> Mycroft: He's from England

The context isn't limited by the keywords provided by the current **Skill**. For example

```python
    @intent_handler(IntentBuilder().require(PythonPerson).require(WhereFrom))
    def handle_from(self, message):
        # PythonPerson can be any of the Monty Python members
        python = message.data.get('PythonPerson')
        self.speak('He is from {}'.format(from_dict[python]))
        self.set_context('PythonPerson', python)
        self.set_context('Location', from_dict[python])
```

Enables conversations with other **Skills** as well.

```text
User: Where is John Cleese from?
Mycroft: He's from England
User: What's the weather like over there?
Mycroft: Raining and 14 degrees...
```

## Using context to enable **Intents**

To make sure certain **Intents** can't be triggered unless some previous stage in a conversation has occured. Context can be used to create "bubbles" of available intent handlers.

```text
User: Hey Mycroft, bring me some Tea
Mycroft: Of course, would you like Milk with that?
User: No
Mycroft: How about some Honey?
User: All right then
Mycroft: Here you go, here's your Tea with Honey
```

```python
from mycroft.skills.context import adds_context, removes_context

class TeaSkill(MycroftSkill):
    @intent_handler(IntentBuilder('TeaIntent').require("TeaKeyword"))
    @adds_context('MilkContext')
    def handle_tea_intent(self, message):
        self.milk = False
        self.speak('Of course, would you like Milk with that?',
                   expect_response=True)

    @intent_handler(IntentBuilder('NoMilkIntent').require("NoKeyword").
                                  require('MilkContext').build())
    @removes_context('MilkContext')
    @adds_context('HoneyContext')
    def handle_no_milk_intent(self, message):
        self.speak('all right, any Honey?', expect_response=True)

    @intent_handler(IntentBuilder('YesMilkIntent').require("YesKeyword").
                                  require('MilkContext').build())
    @removes_context('MilkContext')
    @adds_context('HoneyContext')
    def handle_yes_milk_intent(self, message):
        self.milk = True
        self.speak('What about Honey?', expect_response=True)

    @intent_handler(IntentBuilder('NoHoneyIntent').require("NoKeyword").
                                  require('HoneyContext').build())
    @removes_context('HoneyContext')
    def handle_no_honey_intent(self, message):
        if self.milk:
            self.speak('Heres your Tea with a dash of Milk')
        else:
            self.speak('Heres your Tea, straight up')

    @intent_handler(IntentBuilder('YesHoneyIntent').require("YesKeyword").
                                require('HoneyContext').build())
    @removes_context('HoneyContext')
    def handle_yes_honey_intent(self, message):
        if self.milk:
            self.speak('Heres your Tea with Milk and Honey')
        else:
            self.speak('Heres your Tea with Honey')
```

When starting up only the `TeaIntent` will be available. When that has been triggered and _MilkContext_ is added the `MilkYesIntent` and `MilkNoIntent` are available since the _MilkContext_ is set. when a _yes_ or _no_ is received the _MilkContext_ is removed and can't be accessed. In its place the _HoneyContext_ is added making the `YesHoneyIntent` and `NoHoneyIntent` available.

You can find an example [Tea Skill using conversational context on Github](https://github.com/krisgesling/tea-skill).

As you can see, Conversational Context lends itself well to implementing a [dialog tree or conversation tree](https://en.wikipedia.org/wiki/Dialog_tree).

