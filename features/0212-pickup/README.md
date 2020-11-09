# 0212: Pickup Protocol
- Authors: [Sam Curren](telegramsam@gmail.com)
- Status: [PROPOSED](/README.md#proposed)
- Since: 2019-09-03
- Status Note: Initial version
- Start Date: 2019-09-03
- Tags: [feature](/tags.md#feature), [protocol](/tags.md#protocol)

## Summary

A protocol to coordinate routing configuration between a routing agent and the recipient.

## Motivation

Messages can be picked up simply by sending a message to the _message holder_ with a `return_route` decorator specified. This mechanism is implicit, and lacks some desired behavior made possible by more explicit messages.
This protocol is the explicit companion to the implicit method of picking up messages.

## Tutorial

### Roles

**message_holder** - The agent that has messages waiting for pickup by the _recipient_.
**recipient** - The agent who is picking up messages.
**batch_sender** - A _message_holder_ that is capable of returning messages in a `batch`.
**batch_recipient** - A _recipient_ that is capable of receiving and processing a `batch` message.

### Flow

status can be used to see how many messages are pending.
batch retrieval can be executed when many messages ...

## Reference

### StatusRequest
Sent by the _recipient_ to the _message_holder_ to request a `status` message.
```json=
{
    "@id": "123456781",
    "@type": "https://didcomm.org/messagepickup/1.0/status-request"
}
```
### Status
Status details about pending messages
```json=
{
    "@id": "123456781",
    "@type": "https://didcomm.org/messagepickup/1.0/status",
    "message_count": 7,
    "last_added_time": "2019-05-01 12:00:00Z",
    "last_delivered_time": "2019-05-01 12:00:01Z",
    "last_removed_time": "2019-05-01 12:00:01Z",
    "total_size": 8096
}
```
`message_count` is the only required attribute. The others may be present if offered by the _message_holder_.

- `message_count`: Total message count representing the all the stored message count (wether delivered or not)
- `last_added_time`: A timestamp representing the last time any message stored by the _message_holder_
- `last_delivered_time`: A timestamp representing the last time any message sent by the _message_holder_ to the _recipient_
- `last_removed_time`: A timestamp representing the last time _recipient_ send delete request
- `total_size`: Total message count representing the all the stored message count (wether delivered or not)
- `size_limit`: The maximum message count which can be stored by the _message_holder_ (may be zero for no limit)

### Batch Pickup
A request to have multiple waiting messages sent inside a `batch` message.
```json=
{
    "@id": "123456781",
    "@type": "https://didcomm.org/messagepickup/1.0/batch-pickup",
    "batch_size": 10
}
```

### Batch
A message that contains multiple waiting messages.
```json=
{
    "@id": "123456781",
    "@type": "https://didcomm.org/messagepickup/1.0/batch",
    "messages~attach": [
        {
            "@id" : "06ca25f6-d3c5-48ac-8eee-1a9e29120c31",
            "message" : "{
                ...
            }"
        },

        {
            "@id" : "344a51cf-379f-40ab-ab2c-711dab3f53a9a",
            "message" : "{
                ...
            }"
        }
    ]
}
```
### Message Query With Message Id List
A request to read single or multiple messages with a message message id array.
```json=
{
    "@id": "123456781",
    "@type": "https://didcomm.org/messagepickup/1.0/list-pickup",
    "message_ids": [
        "06ca25f6-d3c5-48ac-8eee-1a9e29120c31",
        "344a51cf-379f-40ab-ab2c-711dab3f53a9a"
        ]
}
```
`message_ids` message id array for picking up messages. Any message id in `message_ids` could be delivered via several ways to the recipient (Push notification or with an envoloped message).
### Message List Query Response
A response to query with message id list.
```json=
{
    "@type": "https://didcomm.org/messagepickup/1.0/list-response",
    "messages~attach": [
        {
            "@id" : "06ca25f6-d3c5-48ac-8eee-1a9e29120c31",
            "message" : "{
                ...
            }"
        },
        {
            "@id" : "344a51cf-379f-40ab-ab2c-711dab3f53a9a",
            "message" : "{
                ...
            }"
        }
    ]
}
```


#### Delete Request
Request message for informing the _message_holder_ for completed messages by the _recipient_ for delete.
```json
{
  "@id": "356c6de8-d126-4812-92a2-9af3bdf1061e",
  "@type": "https://didcomm.org/messagepickup/1.0/delete-request",
  "message_ids": [
        "06ca25f6-d3c5-48ac-8eee-1a9e29120c31",
        "344a51cf-379f-40ab-ab2c-711dab3f53a9a"
  ]
}
```

`message_ids` Message id list for delete


#### Delete Response
Response message for acknowledging the _recipient_ for successfully deleted messages by the _message_holder_ upon a request.
```json
{
  "@id": "356c6de8-d126-4812-92a2-9af3bdf1061e",
  "@type": "https://didcomm.org/messagepickup/1.0/delete-response",
  "message_ids": [
        "06ca25f6-d3c5-48ac-8eee-1a9e29120c31",
        "344a51cf-379f-40ab-ab2c-711dab3f53a9a"
  ]
}
```

`message_ids` Deleted message id list.

### Noop
Used to receive another message implicitly. This message has no expected behavior when received.
```json=
{
    "@id": "123456781",
    "@type": "https://didcomm.org/messagepickup/1.0/noop"
}
```


## Prior art

Concepts here borrow heavily from a [document](https://hackmd.io/@8VtAqKThQ6mKa9T7JgzIaw/SJw9Ead2N?type=view) written by Andrew Whitehead of BCGov.

## Unresolved questions

- We are using multiple roles to indicate which portions of the protocol are supported by each party. This is a new thing we have not done before. Is this ok?

## Implementations

The following lists the implementations (if any) of this RFC. Please do a pull request to add your implementation. If the implementation is open source, include a link to the repo or to the implementation within the repo. Please be consistent in the "Name" field so that a mechanical processing of the RFCs can generate a list of all RFCs supported by an Aries implementation.

Name / Link | Implementation Notes
--- | ---
 |  |
