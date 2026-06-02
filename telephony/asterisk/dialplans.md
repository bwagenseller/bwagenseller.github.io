 # Dialplans  


# Overview  

A <font color="green">Dialplan</font>, also known as a <font color="green">dialing plan</font>, is a set of rules that establishes the permitted sequences of digits dialed by telephone subscribers and the manner in which a telephone switch interprets these digits within the definitions of the prevailing telephone numbering plan. They are used to define an arbitrary number of actions that can be applied to a call.  

A dial plan can include various configurations such as call forwarding, call hold, restrictions, and other features to manage internal and external calls. It can also define how calls are handled when they are not answered or when the line is busy, and it can specify which answering machine should be informed.  

Every entity that can carry audio is, at its base, a [channel](telephony/terms?id=channel). Every channel that arrives on Asterisk's doorstep passes through the <font color="green">dialplan</font>.  

Asterisk's <font color="green">dialplan</font> has its own syntax and is located in `/etc/asterisk/extensions.conf`.  

> There are other ways to control the call flow, but the <font color="green">dialplan</font> is the traditional route.  