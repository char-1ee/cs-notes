# Signals

### SIGTERM vs. SIGKILL

#### SIGTERM (Signal Terminate)

• Signal Number: Typically 15

• Purpose: SIGTERM politely requests a process to terminate. It is the default signal sent by the kill command and other system utilities, such as shutdown.

• Control: Processes can catch, ignore, or handle the SIGTERM signal. This allows a process to perform clean-up operations, such as saving data or releasing resources, before terminating.

• Usage: It is used when you want to give a process the opportunity to end gracefully. If a process is designed to handle SIGTERM, it can ensure that it shuts down in a safe state without risking data corruption.

#### SIGKILL (Signal Kill)

• Signal Number: Typically 9

• Purpose: SIGKILL forcefully terminates a process. The signal cannot be caught, handled, or ignored.

• Control: There is no way for a process to detect or prevent a SIGKILL signal. This means that it does not get the chance to clean up after itself.

• Usage: SIGKILL is used as a last resort when a process does not respond to SIGTERM or other “nicer” methods of termination. It ensures that a process stops executing immediately.

#### Key Differences

• Handling: The biggest difference is that SIGTERM can be intercepted by a process to allow it to terminate gracefully, performing any necessary clean-up tasks. SIGKILL, on the other hand, cannot be caught or ignored, and it does not allow the process any opportunity to tidy up or release resources.

• Safety: Using SIGTERM is generally safer in terms of application stability and data integrity because it respects the process’s internal state and clean-up procedures. SIGKILL might leave shared resources in an inconsistent state or data files partially written.

• Effectiveness: SIGKILL is guaranteed to terminate a process, while a process can choose to ignore or delay handling SIGTERM.

#### Best Practice

It is considered best practice to try terminating a process with SIGTERM first, giving it a chance to end gracefully. If the process does not terminate after a reasonable period, SIGKILL can be used as a fallback to ensure that the process is terminated.

These signals are part of a broader set of system signals that provide ways for the operating system, users, and other processes to communicate with and control running processes.
