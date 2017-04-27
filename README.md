# SRE Challenge

You are tasked with provisioning servers for teams on weekly basis.

Your environment is either virtualized (VMWare) or cloud (AWS).

The servers are typically for a 2 tier web serving application including:
- A set of application servers handling HTTP requests
- A clustered stateful technology that stores required data for the application to handle requests

Each provisioned server would need the following at minimum:
- Allow developers ssh onto them
- Application logs are transferred to a centralized logging system
- Server & application health status are transmitted to a centralized monitoring system
- The latest runtime environment / libraries are installed on the servers


Make assumptions and state them explicitly, pick technologies that you know.
- Do you have a virtualized or cloud environment?
- How is workload spread across the application servers?
- What runtime environment does the application require?
- Which centralized logged system is being used?
- Which centralized monitoring system is being used?
- Which clustered stateful technology is being used? 


What would you do to reduce your time spent on this weekly task?
How would you handle building the entire environment from scratch?
How would you handle adding components to support extra load?

Design your solution in detail, estimate the effort required.
Please implement part of the solution designed above, commit it into a repo and share it with us. 
