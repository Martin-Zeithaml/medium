# The Key Sequences for Zowe TN3270

## Motivation

When working on mainframe environment a lot of commands are being used. Very often these commands are repeating, some environments has retrieve function. But what if I need to repeat a series of commands? And hit enter or press tab between the commands? Or change the name of the dataset for another command? This problem is known and various vendors are using different techniques to achieve this.

## The Key Sequences

The Zowe TN3270 has implemented this feature as **The Key Sequences**. You can define the sequence of key strokes:
* Normal keys - `A`, `B`, ...
* Key modifiers - `Ctrl`, `Alt`, `Shift`
* Function keys - `F1`, `F2`, ...
* Combinations
* Prompt - get the value from input
* Accessible via top right menu bar

![image](https://github.com/Martin-Zeithaml/TN3270-Keys/assets/66114686/9abb1db1-d151-40d0-bb89-f405f8d9dd32)


## Examples

Let's show couple examples and describe what is going on.

### Simple text

As a `SPUFI` user you need to type prefix `SYSIBM.SYS` several times when writing your query. Definition for such key sequence could be:
```
{
  "title": "SYSIBM", "description": "TEXT: SYSIBM.SYS",
  "keys":[
    { "normal": "SYSIBM.SYS" }
  ]
}
```
* The `title` is visible on the Key Sequences bar and the `description` is hover help for that menu item
* `SYSIBM.SYS` will be typed on the current cursor position
  * Note: Zowe TN3270 is not checking if the current cursor position is editable

### Start New ISPF Panel

When using `ISPF` environment, you would be probably using a lot of `START`, `SWAP NEXT`, `P.3.4` and so on. This `ISPF START` example is extended to use special characters. 

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
We already know `title` and `description`, let's focus on the rest:
* `Home` key to get on the command line
  * This depends on your `ISPF` setting, this example suppose command line on the top
* `Ctrl+E` is a defined [keyboard shortcut](https://docs.zowe.org/stable/user-guide/mvd-3270/#keyboard-shortcuts) for Zowe TN3270 to clear from the current cursor position till the end of the field
  * There might be previous command, it is better to delete entire command line
* Type `START` and hit `Enter`

### Prompt Example
Last example is using `prompt` feature to get the input and use it.
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
* `Home` and `Ctrl+E` is already known to us
* `search for '` with opening single parenthesis
* `prompt` is the default browser prompt with provided string
  * The input from this prompt is placed on the current cursor position
* Closing `'` and `Enter`

Prompt example, which will produce `srchfor 'ZIIP'` and hits `Enter`:

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

* Zowe TN3270 offers way to simplify repetitive actions as **Key Sequences**
* Key sequences can be defined by user

## Useful Links

* [User Guide for Zowe TN3270](https://docs.zowe.org/stable/user-guide/mvd-3270/)
* [Directory hierarchy](https://docs.zowe.org/stable/user-guide/mvd-configuration/#configuration-directories)
* [TN3270 Github repository](https://github.com/zowe/tn3270-ng2/)
* [WebUI on Open Mainframe Project Slack](https://openmainframeproject.slack.com/archives/GGGJPMS4W)

Zowe™, and the Zowe™ logo, and the Open Mainframe Project™ are trademarks of the Linux Foundation.
