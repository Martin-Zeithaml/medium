# YAML basic tutorial

As you know, the YAML is used for Zowe configuration from version 2. As this is not usual way how to configure products on mainframe, let's explain basic principles and show some useful tips and tricks.

## Sensitivity

The YAML is case sensitive and indentation sensitive.

### Case sensitivity

Let's demonstrate this on a simple example. Because of case sensitivity, this YAML defines two different elements:
* `config.property` containing a string `hello`
* `config.PROPERTY` containing a string `world`
```yaml
config:
  property: hello
  PROPERTY: world
```

### Indentation sensitivity
One of reason to use YAML is a need to make hierarchical config. This is very important concept with major consequences!
We will reuse the previous example to demonstrate the indentation, as we know there is defined `config.property` and `config.PROPERTY`:

If we change the indentation of `PROPERTY`, we will get different configuration.
```yaml
config:
  property: hello
PROPERTY: world
```
It is better to explain it on the JSON, as it is using curly braces to enclose the objects:

```json
{
  "config": {
    "property": "hello"
  },
  "PROPERTY": "world"
}
```

Now we have `config.property` and `PROPERTY` - in other words, we have moved to `PROPERTY` to the same level as `config`.

### Indentation again

Now it gets little bit complicated, the indentation is not strictly constant, it can vary. Following example shows, the `setup` has two another properties and each of them has a `debug`. Despite the different indentations, this is valid YAML.

```yaml
setup:
  opt1:
   debug: true
  opt2:
        debug: false
```

However adding new property means to honor current indentation as on this example:
```yaml
setup:
  opt1:
   debug: true
   logFile: /dev/null
  opt2:
        debug: false
        logFile: /dev/null
```


## Best practice

The best practice is to always use 2 spaces, due to being the default value for many modern editors and ISPF editor. 

## How to effectively update example-zowe.yaml

When installing and configuring Zowe, you need to prepare the YAML configuration. The easy practice is to take provided file `example-zowe.yaml` and start editing it. We will show some problems and tips & tricks on the following part of the config:

```yaml
zowe:

  #-------------------------------------------------------------------------------
  # These configurations are used by "zwe install" or "zwe init" commands.
  #-------------------------------------------------------------------------------
  setup:
    # MVS data set related configurations
    dataset:
      # **COMMONLY_CUSTOMIZED**
      # where Zowe MVS data sets will be installed
      prefix: IBMUSER.ZWEV3
      # **COMMONLY_CUSTOMIZED**
      # PROCLIB where Zowe STCs will be copied over
      proclib: USER.PROCLIB
      # **COMMONLY_CUSTOMIZED**
      # Zowe PARMLIB
      parmlib: IBMUSER.ZWEV3.CUST.PARMLIB
      # Holds Zowe PARMLIB members for plugins
      parmlibMembers:
        # For ZIS plugins
        zis: ZWESIP00
      # **COMMONLY_CUSTOMIZED**
      # JCL library where Zowe will store temporary JCLs during initialization
      jcllib: IBMUSER.ZWEV3.CUST.JCLLIB
      # Utilities for use by Zowe and extensions
      loadlib: IBMUSER.ZWEV3.SZWELOAD
      # APF authorized LOADLIB for Zowe
      authLoadlib: IBMUSER.ZWEV3.SZWEAUTH
      # **COMMONLY_CUSTOMIZED**
      # APF authorized LOADLIB for Zowe ZIS Plugins
      authPluginLib: IBMUSER.ZWEV3.CUST.ZWESAPL

    # >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
    # # Security related configurations. This setup is optional.
    # security:
    #   # security product name. Can be RACF, ACF2 or TSS
    #   product: RACF
    #   # security group name
    #   groups:
    #     # Zowe admin user group
    #     admin: ZWEADMIN
    #     # Zowe STC group
    #     stc: ZWEADMIN
    #     # Zowe SysProg group
    #     sysProg: ZWEADMIN
    #   # security user name
    #   users:
    #     # Zowe runtime user name of main service
    #     zowe: ZWESVUSR
    #     # Zowe runtime user name of ZIS
    #     zis: ZWESIUSR
    #   # STC names
    #   stcs:
    #     # STC name of Zowe main service
    #     zowe: ZWESLSTC
    #     # STC name of Zowe ZIS
    #     zis: ZWESISTC
    #     # STC name of Zowe ZIS Auxiliary Server
    #     aux: ZWESASTC
```

Note: the `example-zowe.yaml` is prepared with default indentation of 2 spaces.

### Common mistake

If you manually delete just the comment character `#` starting at line `# security` to the last line, you will get this error, when try to use the config:
```
/zowe/bin: ./zwe config validate -c ./medium-example.yaml
ZWEL0318E - Couldn't parse file '/zowe/bin/medium-example.yaml': while parsing a block mapping at line 3, column 5, did not find expected key at line 30, column 6.
Error: Could not load config for FILE(./medium.yaml):FILE(/zowe/files/defaults.yaml), status=7
```
This error is cause by wrong indentation of `security` section and it is not much helpful for unexperienced users.

### Editor with comments feature

We can do the same edit with correct result. Many modern editors has a comment feature, which is able to comment or uncomment a code. The following demo is from the VScode, you will simply select the section and hit `Crtl+/`. This will uncomment the section, but entire section is move 2 characters to the left, which has the same indentation as `zowe.setup.dataset`, which is desired.

![edit1](https://github.com/user-attachments/assets/adaaa61c-a1eb-45bc-bc6b-2c796a435c1a)

### Options in ISPF

Note: C language syntax is quite useful for YAML: the comments are treated as preprocessor statements and displayed in different color then the YAML. This setting (`HILITE C`) was set for the following demos.

If you prefer the `ISPF` editor, you can achieve the same result with existing features of `ISPF` and your terminal application. In this example, we have used the line command (`((2`) to shift the text in a block by 2 characters left and then we have selected and deleted all the comment characters:

![edit2](https://github.com/user-attachments/assets/0cfac6f0-107a-49f8-96ee-df503ddcb275)


### Own ISREDIT macro

You can write ISREDIT macro to manipulate the text, for example:

```rexx
/*-----REXX-----------------------------------------------------------*/
/*                                                                    */
/*   YAML: Comment or uncomment in VSCode style                       */
/*                                                                    */
/*   Y, YY or Yn to mark start and end.                               */
/*   Then type YAML command.                                          */
/*                                                                    */
/*   Expected: This in SYSEXEC                                        */
/*                                                                    */
/*--------------------------------------------------------------------*/
address ISREDIT
  "MACRO NOPROCESS"
  "PROCESS RANGE Y"
if rc = 0 then do
  "(first) = LINENUM .ZFRANGE"
  "(last) = LINENUM .ZLRANGE"
/*--------------------------------------------------------------------*/
/* First pass: check comments and min indentation                     */
/*--------------------------------------------------------------------*/
  comments = 0
  do i = first to last
    "(config) = LINE (i)"
    if substr(strip(config), 1, 1) = '#' then
      comments = comments + 1
    currentIndent = length(config) - length(strip(config, 'L'))
    if i = first then
      minIndent = currentIndent
    if minIndent > currentIndent then
      minIndent = currentIndent
  end
/*--------------------------------------------------------------------*/
/* Decide to comment or uncomment                                     */
/*--------------------------------------------------------------------*/
  if (last - first + 1) = comments then
    comments = 1
  else
    comments = 0
/*--------------------------------------------------------------------*/
/* Second pass: either delete or add comment char(s)                  */
/*--------------------------------------------------------------------*/
  do i = first to last
    "(config) = LINE (i)"
    if comments = 1 then do
      delete = 1
      if pos('#', config) = pos('# ', config) then
        delete = 2
      newLine = delstr(config, pos('#', config), delete)
      "LINE "i" = (NEWLINE)"
    end
    else do
      newLine = insert('# ', config, minIndent)
      "LINE "i" = (NEWLINE)"
    end
  end
end
```

The usage is quite simple, you will use defined line command (`Y` in this case) to mark the lines and call the macro, which is named `YAML`:

![edit3](https://github.com/user-attachments/assets/c3dcd75e-a7a1-4836-8776-e9b0b7dcffaa)

