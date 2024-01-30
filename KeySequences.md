# The Key Sequences for Zowe TN3270

When working in a mainframe environment, the user can become somewhat overwhelmed with the number of commands that can be used. Very often, in fact, commands are repeating. Some environments also have the retrieve function. Developers familiar with working in a mainframe environment know that part of a regular workflow can be repeating a series of commands over and over again, often times requiring the developer to hit Enter or Tab between commands. And what about changing the name of the dataset for another command? Such workflow processes are common practice, which has prompted various vendors to come up with a range of functionalities to address these user needs.

## Introducting Key Sequences for Zowe TN3270

In the case of Zowe TN3270, the solution to addressing these workflow requirements can be found in the **Key Sequences** feature. With Key Sequences, the user can define the sequence of key strokes that apply to the following categories:

* Normal keys - `A`, `B`, ...
* Key modifiers - `Ctrl`, `Alt`, `Shift`
* Function keys - `F1`, `F2`, ...
* Combinations
* Prompt - get the value from input
* Accessible via top right menu bar

![image](https://github.com/Martin-Zeithaml/TN3270-Keys/assets/66114686/9abb1db1-d151-40d0-bb89-f405f8d9dd32)

### The benefits of using Key Sequences
There are a range of benefits to using these Key Sequences including the following:
* The definition is in JSON format which makes it easy to read, change and share. 
* The Key Sequences is a time saving feature, whereby you can define repeating or complex commands.
* Also, as Zowe is developed as an open-source solution, new ideas for extending this functionality are always welcome!

Let's go into a few examples of Key Sequences and describe what's going on.

## Using Key Sequences with Simple Text

In the example of simple text, as a `SPUFI` user, it is necessary to type the prefix `SYSIBM.SYS` several times when writing your SQL query. To address this, the user could define the key sequence like this:
```
{
  "title": "SYSIBM", "description": "TEXT: SYSIBM.SYS",
  "keys":[
    { "normal": "SYSIBM.SYS" }
  ]
}
```
The `title` is visible on the Key Sequences bar and for the `description`. The user can then hover to view the help menu which can present choices to choose from. In this case, `SYSIBM.SYS` is presented on the current cursor position. The behavior of this simple definition is like a clipboard. By using Key Sequences, you can define more options, which can be  readily available through the hovering pop-up menu. 

Note that Zowe TN3270 does not check if the current cursor position is editable.

### Start New ISPF Panel

Another application of the Key Sequences feature is to start a new ISPF panel. When using the `ISPF` environment, one probably will be using commands such as `START`, `SWAP NEXT`, `P.3.4` and so on. This `ISPF START` example is extended to use special characters. 

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
* The `Home` key is to get you on the command line.
  * This depends on your `ISPF` setting. In this example, let's suppose that the command line is on the top. For other placements, you might need to alter the `Home` setting to get on the command line.
* `Ctrl+E` is a defined [keyboard shortcut](https://docs.zowe.org/stable/user-guide/mvd-3270/#keyboard-shortcuts) for Zowe TN3270 to clear from the current cursor position until the end of the field.
  * There might, however, be a previous command, in which case it is better to delete the entire command line using predefined keyboard shortcut `Ctrl+E`.
* Type `START` and hit `Enter`.

Having this key sequence definition can be very helpful, as you can invoke it regardless of the cursor position or the text presented on the command line. The result will be always the same, and a new `ISPF` panel will be opened. 

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

In this case, use the `ISPF` command `srchfor` to find text in all members in the current library:
* `srchfor '` with opening apostrophe
* `prompt` is the default browser prompt with provided string
  * The input from this prompt is placed on the current cursor position
* Closing `'` and `Enter`

Here's a Prompt example, which will produce `srchfor 'ZIIP'` and `Enter`:

![image](https://github.com/Martin-Zeithaml/TN3270-Keys/assets/66114686/d732aeb7-c12b-4eed-a7cf-e4d46d12d97d)

This definition has couple benefits. First of all, you don't need to remember how to spell the `ISPF` command `srchfor`. Secondly, by using the user's input to create this simple query with the use of opening and closing apostrophes, you don't need to worry about spaces (`srchfor` requires one string as a parameter; if such string contains space(s), it must be enclosed in apostrophes).

## Defaults and Location

* Defaults
  * [_keySequences.json](https://github.com/zowe/tn3270-ng2/blob/v2.x/staging/config/storageDefaults/sessions/_keySequences.json)
  * `Erase Field`, `Erase from cursor`, `Clear`
  * `Reset`
  * `PA1`, `PA2` and `PA3` 
  * `PF13` to `PF24`
* Location of `_keySequences.json` file
  * There is a [hierarchy of directories](https://docs.zowe.org/stable/user-guide/mvd-configuration/#configuration-directories)
  * Example for the user: `{zowe.workspaceDirectory}/app-server/users/{userID}/ZLUX/pluginStorage/org.zowe.terminal.tn3270/sessions`
* How to define keys
  * Corresponds to JavaScript [Event.key](https://www.toptal.com/developers/keycode)

We have explained three examples to introduce the basic principles of Key Sequences. You can use this knowledge to modify and extend your Key Sequences to meet your specific requirements.

For a final example, let's look at the full `_keySequences.json`:

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
## Take-away from leveraging Key Sequences 

Zowe TN3270 offers a straightforward way to simplify repetitive actions in the form of **Key Sequences**, which can be defined by the user to save time and improve the user's workflow. 

## Useful Links

For more information about this functionality, see the following links:

* [User Guide for Zowe TN3270](https://docs.zowe.org/stable/user-guide/mvd-3270/)
* [Directory hierarchy](https://docs.zowe.org/stable/user-guide/mvd-configuration/#configuration-directories)
* [TN3270 Github repository](https://github.com/zowe/tn3270-ng2/)
* [WebUI on Open Mainframe Project Slack](https://openmainframeproject.slack.com/archives/GGGJPMS4W)

Zowe™, and the Zowe™ logo, and the Open Mainframe Project™ are trademarks of the Linux Foundation.
