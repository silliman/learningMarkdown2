# Building the Documentation Yourself

!!! warning "For Documentation Contributors Only"

    This is for people who want to update the documentation of `Confidential Computing LinuxONE Workshop` and see the updates locally or if you are a regular user and are curious on how to do it. In most cases, just accessing the documentation via this site (https://ibm-wsc.github.io/ConfidentialComputingOnLinuxONE/) is best. If you want to access the documentation without internet access, instead of following this documentation, you should navigate to the print page (which displays the documentation in printable format). Then, Print->Save to pdf in your web browser and access the PDF freely offline.

## Installing Necessary Pre-requisites

1. Install pip3 on your computer, if it's not already installed (instructions vary depending on Operating System)
2. Install mkdocs and plugins used in `Confidential Computing LinuxONE Workshop` documentation

    ``` bash
    pip3 install mkdocs mkdocs-material mkdocs-git-revision-date-localized-plugin mkdocs-print-site-plugin
    ```

3. Get a local copy of the source code with:

    === "HTTPS"

        ``` bash
        git clone https://github.com/ibm-wsc/ConfidentialComputingOnLinuxONE.git
        ```

    === "SSH"

        ``` bash
        git clone git@github.com:ibm-wsc/ConfidentialComputingOnLinuxONE.git
        ```

4. Change into the source code directory in your terminal

5. Serve the documentation in a terminal while in your source code directory with:

    ``` bash
    mkdocs serve
    ``` 

6. Open a web browser and access the documentation

    The above `mkdocs serve` command should output an address to access the documentation which by default is: http://127.0.0.1:8000 which is over localhost (only accessible locally). Go to this address to access the documentation.

    !!! Tip "Documentation updates automatically"

        If you leave the terminal tab where you ran `mkdocs serve` open, the docs will update automatically when your save your changes to the file. This way as you make changes you can check your changes at the given web site, displaying in the same way as it will when displayed as a static website on GitHub.

7. Make edits to the relevant markdown files in the `docs/` subfolder, and watch the changes display in realtime in your web browser when you save your file updates.

!!! Tip "Special Documentation Features"

    To use the special features of `mkdocs-material` and the plugins used in these docs that enhance the documentation from traditional markdown, please take a look at the syntax for the various features. You can find examples of them in action in the markdown of this site by either:
    
    - Inspecting a given page of the site in your web browser

        1. scroll to the top of a page with a feature you want to see how to use
        2. click the page with an eye icon to the right of the title
        3. Inspect the markdown for the part of the page with the given feature

    OR

    - See the markdown for the entire site in the `docs/` subfolder of the GitHub project, navigating the markdown files for each page
        
    Additionally, you can find more examples and explainers on the [mkdocs-material reference page](https://squidfunk.github.io/mkdocs-material/reference/){target="_blank" rel="noopener"}.