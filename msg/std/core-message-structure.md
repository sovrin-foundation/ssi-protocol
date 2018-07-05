- Name: core-message-structure
- Start Date: July 5th, 2018

# Summary
[summary]: #summary

This document describes the core message structure for agent-to-agent communication.

# Motivation
[motivation]: #motivation

Establishing a core message structure will allow developers implementing agents to ensure interoperability in at least
the general structure of messages.

# Tutorial
[tutorial]: #tutorial

The following `json`-like object is a representation of the proposed core message structure before being packaged and
sent over the transport layer or after it is received through the transport layer and unpackaged. However, using `json`
for messages is not necessarily part of this proposal.

```json
{
  "type": "URN message type",
}
```

- The type attribute is required and is a type string as outlined by a planned HIPE for message types.

- Depending upon the type of message listed in the type, there will be additional key:value pairs that are defined in the messsage family. Please refer to the message family for defined additional key:value pairs associated with the type.


# Reference
[reference]: #reference

This structure has been discussed in community calls for agent development. Much of this discussion has been collected and added to [this Google Doc](https://docs.google.com/document/d/1mRLPOK4VmU9YYdxHJSxgqBp19gNh3fT7Qk4Q069VPY8).

# Drawbacks
[drawbacks]: #drawbacks

Up to this point, no drawbacks for this core message structure have been identified.

# Rationale and alternatives
[alternatives]: #alternatives

At this point, just having a message structure outlined will continue to facilitate development of agents. By introducing this structure, necessary modifications will hopefully come to light as agent development continues.

