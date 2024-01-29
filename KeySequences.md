# The Key Sequences for Zowe TN3270

When working in a mainframe environment, the user can become somewhat overwhelmed with the number of commands that can be used. Very often, in fact, commands are repeating. Some environments also have the retrieve function. But what if I need to repeat a series of commands. What if I'm required to hit enter or press tab between the commands? And what about changing the name of the dataset for another command? Such problems are common and various vendors use a range of techniques to enable finctionalities to address these problems.

## The Key Sequences

In the case of Zowe TN3270, this feature is called the **Key Sequences**, in which the user can define the sequence of key strokes that apply to the following categories:

* Normal keys - `A`, `B`, ...
* Key modifiers - `Ctrl`, `Alt`, `Shift`
* Function keys - `F1`, `F2`, ...
* Combinations
* Prompt - get the value from input
* Accessible via top right menu bar

![image](https://github.com/Martin-Zeithaml/TN3270-Keys/assets/66114686/9abb1db1-d151-40d0-bb89-f405f8d9dd32)


Let's go into a few examples and describe what's going on.

In the example of simple text, as a `SPUFI` user, it is necessary to type the prefix `SYSIBM.SYS` several times when writing your query. The definition for such key sequence could be:
```
{
  "title": "SYSIBM", "description": "TEXT: SYSIBM.SYS",
  "keys":[
    { "normal": "SYSIBM.SYS" }
  ]
}
```
The `title` is visible on the Key Sequences bar and for the `description`, and there is hover help for that menu item whereby `SYSIBM.SYS` will be typed on the current cursor position.

Note that Zowe TN3270 does not check if the current cursor position is editable.

### Start New ISPF Panel

Another application is to start a new ISPF panel. When using `ISPF` environment, chances are you will be using commands such as `START`, `SWAP NEXT`, `P.3.4` and so on. This `ISPF START` example is extended to use special characters. 

```
{
   "title": "START", "description": "ISPF: START new ISPF panel",
   "keys":[
      { "special": "Home" },
      { "normal": "E", "ctrl": "true" },
      { "normal": "START" },
      { "special": "Enter" }
   ]
}
```
We already know `title` and `description`, so let's focus on the rest of the keys:
* The `Home` key is to get on the command line.
 This depends on your `ISPF` setting. In this example, let's suppose that the command line on the top
* `Ctrl+E` is a defined [keyboard shortcut](https://docs.zowe.org/stable/user-guide/mvd-3270/#keyboard-shortcuts) for Zowe TN3270 to clear from the current cursor position until the end of the field.
  * There might, however, be a previous command, in which case it is better to delete the entire command line. 
* Type `START` and hit `Enter`.

<!-- I think you need to describe the end result and summarize the benefit. I don't see the purpose of this described. -->

### Prompt Example
As a final example, let's use the `prompt` feature to get input and put it to use.
```
{
   "title": "Srchfor", "description": "ISPF: srchfor 'string'",
   "keys":[
      { "special": "Home" },
      { "normal": "E", "ctrl": "true" },
      { "normal": "srchfor '"},
      { "prompt": "String to find" },
      { "normal": "'" },
      { "special": "Enter" }
  ]
}
```
So, we already know `Home` and `Ctrl+E`.

In this case, use `search for` to <!-- Explain what is being searched -->
* `search for '` with opening single parenthesis
* `prompt` is the default browser prompt with provided string
  * The input from this prompt is placed on the current cursor position
* Closing `'` and `Enter`

Here's a Prompt example, which will produce `srchfor 'ZIIP'` and `Enter`:

![image](https://github.com/Martin-Zeithaml/TN3270-Keys/assets/66114686/d732aeb7-c12b-4eed-a7cf-e4d46d12d97d)

## Defaults and Location

* Defaults
  * [_keySequences.json](https://github.com/zowe/tn3270-ng2/blob/v2.x/staging/config/storageDefaults/sessions/_keySequences.json)
  * `Erase Field`, `Erase from cursor`, `Clear`
  * `Reset`
  * `PA1`, `PA2` and `PA3` 
  * `PF13` to `PF24`
* Location of `_keySequences.json` file
  * There is a [hierarchy of directories](https://docs.zowe.org/stable/user-guide/mvd-configuration/#configuration-directories)
  * For example for the user: `{zowe.workspaceDirectory}/app-server/users/{userID}/ZLUX/pluginStorage/org.zowe.terminal.tn3270/sessions`
* How to define keys
  * Corresponds to JavaScript [Event.key](https://www.toptal.com/developers/keycode)

## Example
This example as `_keySequences.json`:
```
{
   "keySequences" :[
      {
         "title": "SYSIBM", "description": "TEXT: SYSIBM.SYS",
         "keys":[
            { "normal": "SYSIBM.SYS" }
         ]
      },
      {
         "title": "START",
         "description": "ISPF: START new ISPF panel",
         "keys":[
            { "special": "Home" },
            { "normal": "E", "ctrl": "true" },
            { "normal": "START" },
            { "special": "Enter" }
         ]
      },
      {
         "title": "Srchfor",
         "description": "ISPF: srchfor 'string'",
         "keys":[
            { "special": "Home" },
            { "normal": "E", "ctrl": "true" },
            { "normal": "srchfor '"},
            { "prompt": "String to find" },
            { "normal": "'" },
            { "special": "Enter" }
         ]
      }
   ]
}
```
## Summary

* Zowe TN3270 offers way to simplify repetitive actions in the form of **Key Sequences**, which can be defined by the user.

## Useful Links

For more information about this functionality, see the following links:

* [User Guide for Zowe TN3270](https://docs.zowe.org/stable/user-guide/mvd-3270/)
* [Directory hierarchy](https://docs.zowe.org/stable/user-guide/mvd-configuration/#configuration-directories)
* [TN3270 Github repository](https://github.com/zowe/tn3270-ng2/)
* [WebUI on Open Mainframe Project Slack](https://openmainframeproject.slack.com/archives/GGGJPMS4W)

Zowe™, and the Zowe™ logo, and the Open Mainframe Project™ are trademarks of the Linux Foundation.
