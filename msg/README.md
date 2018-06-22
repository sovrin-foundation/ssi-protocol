# ssi-protocol/msg

This folder holds docs on different messages that might be exchanged.
(Communication in SSI is assumed to be fundamentally message-based, though
there are ways to adapt it to streaming, multicast, and similar use cases.)
The `/msg` folder is further subdivided:
  
  * [`/msg/std`](std/README.md) -- Standard messages of general interest,
  foundational to any SSI work. For example, an error message, messages to
  query and reply about the capabilities of another entity, messages about
  issuing credentials or proving things, or messages about how to route
  other messages. Messages are organized into __message families__ by subdir
  -- so there might be a subdir called `cred_issuance`, for example.
  
  * [`/msg/ext`](ext/README.md) -- Message families of narrower interest, not
  necessary for general interop. For example, messages to control IoT sensors
  might go here. 