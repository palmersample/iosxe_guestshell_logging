# iosxe_guestshell_logging

Example code and wrapper for Python's built-in `logging` package to enable syslog capture of logging messages on devices running Cisco IOS XE Software.

This implementation was based on information in the [Programmability Configuration Guide, Cisco IOS XE Amsterdam 17.3.x](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/prog/configuration/173/b_173_programmability_cg/guest_shell.html).

## Getting Started

1. Enable IOx and configure the `guestshell` application using the Programmability Configuration Guide.

2. Enter the `bash` prompt from the network device:

    <pre># <b>guestshell run bash</b></pre>
    
3. Create a Python Virtual Environment inside the guestshell. *Note: The system-installed Python installation includes Cisco-specific Python packages such as `eem` and `cli`. To ensure those packages are available, use the `--system-site-packages` argument to the Python `venv` module, as shown below:*

    ```bash
    python3 -m venv --system-site-packages venv
    ```

4. Activate the virtual environment:

    ```bash
    source venv/bin/activate
    ```

5. Install this Python package, using `pip`:

    ```bash
    pip install iosxe_guestshell_logging
    ```

6. Add the following lines to /home/guestshell/.bashrc to ensure the virtual environment's Python interpreter is loaded by default when a script is launched from Privileged Exec mode:

    ```bash
   cat >> ~/.bashrc << EOF
   VIRTUAL_ENV=/home/guestshell/venv
   if [ -d ${VIRTUAL_ENV} ]; then
       PATH="${VIRTUAL_ENV}/bin:${PATH}"
       export VIRTUAL_ENV
       export PATH
   fi
   EOF

    ```

7. Exit guestshell to return to the Cisco IOS XE privileged exec prompt:

    ```bash
   exit
    ```

8. Verify the desired Python interpreter is executed when started from the device prompt:

    <pre>rtr# <b>guestshell run which python</b></pre>
   
    The command should return:

    <pre>/home/guestshell/venv/bin/python</pre>

## Usage

After you create the virtual environment, update the .bashrc file, and verify the interpreter path, implementation is simple. Example code for logging to different syslog levels is as follows:

```python
from iosxe_guestshell_logging import configure_guestshell_logging

logger = configure_guestshell_logging(__name__)

logger.critical("GuestShell example: Critical message (level 2)")
logger.error("GuestShell example: Error message (level 3)")
logger.warning("GuestShell example: Warning message (level 4)")
logger.notice("GuestShell example: Notice message (level 5)")
logger.info("GuestShell example: Info message (level 6)")
logger.debug("GuestShell example: Debug message (level 7)")
```

You can view the generated messages directly on the device console, in the logging monitor (ensure your device logging level is set appropriately), or via the `show logging` command on the device. In all cases, ensure the device logging level is configured appropriately for console, monitor, or buffered, as appropriate.

## Enjoy!

Now, instead of using `print` statements in your code or watching `logging` messages vanish, you can capture the output of your own guestshell scripts in syslog!
