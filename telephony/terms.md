# Telephony Terms  

# Bridge  

A <font color="green">bridge</font> is an entity that connects multiple [channels](telephony/terms?id=channel). For example, think of a channel as a person; the <font color="green">bridge</font> is a conference room that the people (read: channels) are standing in. People inside the conference room can hear the conversation - and talk to others in the room - but people outside of the conference room cannot hear the conversation.  


# Call Data Record (CDR)  

A <font color="green">Call Data Record</font> (<font color="green">CDR</font>) is a document that gives some information about the call.  


# Channel  

A <font color="green">channel</font> is an object that encapsulates audio streams for exactly one entity / person. For example, a phone call that has two people talking has exactly two channels - one for each person. The channel facilitates the audio going _to_ the person (i.e. so they can hear) as well as audio coming _from_ the person (i.e. transmitting what they are saying).  

# Codec  

A <font color="green">codec</font>, which stands for compressor-decompressor or coder-decoder, is a tool used for compressing and decompressing data streams or signals, making it easier to store and transmit video and audio files. There are many various codec algorithms that exist for audio and video streams.  

# Dialplan  

A <font color="green">dialplan</font>, also known as a <font color="green">dialing plan</font>, is a set of rules that establishes the permitted sequences of digits dialed by telephone subscribers and the manner in which a telephone switch interprets these digits within the definitions of the prevailing telephone numbering plan. They are used to define an arbitrary number of actions that can be applied to a call.  

A dial plan can include various configurations such as call forwarding, call hold, restrictions, and other features to manage internal and external calls. It can also define how calls are handled when they are not answered or when the line is busy, and it can specify which answering machine should be informed.  

# Private Branch Exchange  

A <font color="green">private branch exchange</font> (<font color="green">PBX</font>) is a private telephone network that facilitates internal and external communication.  

# PSTN  

<font color="green">PSTN</font> (<font color="green">Public Switch Telephone Network</font>) is the circuit-based network that was behind landline phones since the late 1800s. Unlike VoIP, this is _not_ IP-based. 

# RTP  

<font color="green">RTP</font> (<font color="green">Real Time Protocol</font>) is a protocol used alongside SIP that delivers the audio during a SIP session.  

# SDP  

<font color="green">SDP</font> (<font color="green">Session Description Protocol</font>) is a protocol used alongside SIP that delivers information about the session (i.e. codecs, etc).  

# SIP  

<font color="green">SIP</font> (<font color="green">Session Initiation Protocol</font>) is the protocol behind VoIP. Its main function is for signaling and establishing a session. 

Other fun facts: 
* Its default port is 5060  
* Once the session is established, a different protocol - RTP - is used to deliver the audio.  
* Uses a different protocol - SDP - to carry information about the call (i.e. codecs, etc)  

# Station  

A <font color="green">station</font> is another word for an entity connected to a [PBX](telephony/terms?id=private-branch-exchange); usually, this is a telephone but it can be anything that can communicate with the PBX.  


# Trunk  

A <font color="green">trunk</font> is a resource that connects to the outside world, i.e. beyond the confines of a [PBX](telephony/terms?id=private-branch-exchange).  

