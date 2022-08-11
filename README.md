## Heuristics for Vendoring MIT Licensed Code

Disclaimer: I am not a lawyer, but I've helped maintain a dependency manager for a decade and this is stuff that comes up.

> The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

Is what you are trying to conform to in the MIT license.

Prefer video? [An overview of different code licenses](https://www.youtube.com/watch?v=0r7JcN3Q_LY) and how to use them in your code in a talk I gave at Artsy 2015. There's a few cool new licenses since then, but nothing you would be using in everyday code.

--

So, you want to vendor some code. There are roughly 5 ways in which you would do it. It doesn't matter if your code is OSS or closed-source, as the license still applies:

1. **Let your package manager do it for you**. You're always doing this all the time. This works because the package manager includes the license file or references it in the package manifest. 

   Not all package managers ensure licensing info to be fair though, so even then you may need to _manually_ verify the license is referenced in their code (I used to use [danger-plugin-yarn](https://github.com/orta/danger-plugin-yarn) to highlight this) 

  
2. **You are copying a few lines of code** or a function. Add a reference comment going back to the original:

    ```ts
    // Originally taken from shikijs because it was not exported, I've sent a PR upstream
    // https://github.com/shikijs/shiki/blob/33ae6b97eda78484460ac05b3338bc72a605194a/packages/shiki/src/highlighter.ts#L210
    // License MIT: https://github.com/shikijs/shiki/blob/33ae6b97eda78484460ac05b3338bc72a605194a/LICENSE

    function isPlaintext(lang: string | null | undefined) {
        return !lang || ['plaintext', 'txt', 'text'].includes(lang)
    }
    ```

    > Note: this is what you're meant to do with code stack overflow code.

    > Note 2: Referencing the git commit and the original LOC is optional, you **only** need the reference to the license. That said, licenses can change - so this is future-proofing.

    ```ts
    // Originally from shikijs https://github.com/shikijs/shiki/blob/main/LICENSE

    function isPlaintext(lang: string | null | undefined) {
        return !lang || ['plaintext', 'txt', 'text'].includes(lang)
    }
    ```

    Is totally OK.


3. **You are copying a whole file**. You can either: 
   1. Add a reference comment going back to the original. For example in shikijs we have auto-updating themes based on other people's repos. What we do there is:

        ```json
        {
        "_copyright": "The MIT License (MIT)\nCopyright (c) 2015-2022 spgennard\nSource: https://github.com/spgennard/vscode_cobol/blob/main/syntaxes/COBOL.tmLanguage.json",
        "$schema": "https://raw.githubusercontent.com/spgennard/vscode_cobol/main/schemas/tmlanguage.json",
        "fileTypes": [
            "ccp",
            "scbl",
            "cobol",
            
        ```
    2. Make a folder which includes a full copy of the license, and that specific file:

        ```
        tree .

        vendor/COBOL
        ├── LICENSE
        └── COBOL.tmLanguage.json
        ```
        
      I'd recommend making sure that the license file contains a HTTP link to the original repo also, if it doesn't add a new file for it.


4. **You took some code but you changed it so much** that you just want to cover your butt. I did this for [RedwoodJS here](https://github.com/redwoodjs/redwood/blob/0e9754beaaee7fea21cb4f85027f49a41a679795/packages/web/src/components/DevFatalErrorPage.tsx) after taking a library's code, re-writing it to React, making it make sense for Redwood while still kept note of its origins and license:

    ```ts
    // This file is a hard fork of panic-overlay for RedwoodJS. The original code
    // is licensed under The Unlicense - https://github.com/xpl/panic-overlay/blob/master/LICENSE
    // making it fine for embedding inside this project.

    import { useState } from 'react'
    ```

5. **You have a substantial set of files, or are vendoring a whole project**. You want to put it in its own folder, ensure there is the original license and a link to the original codebase and what commit you took it from (I think you can technically argue that `git submodules` handles this referential part for you if you use that) but something like:

    Then it is totally enclosed, and obvious where this code has come from and who was the original authors:

    ```sh
    tree .

    vendor/shikijs-monochrome
    ├── LICENSE
    ├── index.ts
    └── README.md
    ```

    For MIT you _do not_ need to keep track of changes (some licenses do, in those cases use a repo fork with git submodules IMO) you just keep the original reference around.