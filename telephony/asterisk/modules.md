# Asterisk Modules  

This section will discuss Asterisk [modules](telephony/asterisk/asterisk_basics?id=module).  

# Module File Locations  

* Master module file: `/etc/asterisk/modules.conf`  
* Extension file: `/etc/asterisk/extensions.conf`  
* Module install location: `/usr/lib/asterisk/modules`  
  * You probably wont interact with this much  

# Popular Application Modules  

> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

| Name | Description |  
| --- | --- |  
| app_authenticate | Compares dual-tone multifrequency (DTMF) input against a provided string (password) |  
| app_cdr | Writes ad hoc record to CDR |  
| app_chanspy | Allows a channel to listen to audio on another channel |  
| app_confbridge | Provides conferencing |  
| app_dial | Used to connect channels together (i.e., make phone calls) |  
| app_directed_pickup | Answers a call that’s ringing at another extension |  
| app_directory | Presents the list of names from voicemail.conf |  
| app_dumpchan | Dumps channel variables to Asterisk command-line interface (CLI) |  
| app_echo | Echos received audio back to source channel (can be helpful in demonstrating latency) |  
| app_exec | Contains Exec(), TryExec(), and ExecIf(): executes a dialplan application conditionally |  
| app_mixmonitor | Records both sides of a call (transmit and receive) and mixes them together into a single file |  
| app_originate | Allows dialplan logic to originate a call (as opposed to a call coming in on a channel) |  
| app_page | Creates multiple audio connections to specified devices for public address (paging) |  
| app_parkandannounce | Enables automated announcing of parked calls |  
| app_playback | Plays a file to the channel (does not accept input) |  
| app_playtones | Plays pairs of tones of specified frequencies (DTMF mostly) |  
| app_queue | Provides Automatic Call Distribution (ACD) |  
| app_read | Requests input of digits from callers and assigns input to a variable |  
| app_readexten | Requests input of digits from callers and passes call to a designated extension and context |  
| app_record | Records received audio to a file |  
| app_senddtmf | Transmits DTMF to calling party |  
| app_stack | Provides GoSub(), GoSubIf(), Return(), StackPop(), LOCAL(), and LOCAL_PEEK() |  
| app_stasis | Passes call control to an ARI application—many Asterisk developers use this one application, and from there handle all the rest of their development outside of the Asterisk dialplan |  
| app_system | Executes commands in a Linux shell |  
| app_transfer | Performs a transfer on the current channel |  
| app_voicemail | Provides voicemail |  
| app_while | Includes While(), EndWhile(), ExitWhile(), and ContinueWhile(); provides while loop functionality in the dialplan |  

# Popular Bridging Modules  

> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

!> Note, these are only valid with app_confbridge.  

| Name | Description |  
| --- | --- |  
| bridge_builtin_features | Performs bridging when utilizing built-in user features (such as those found in features.conf). |  
| bridge_multiplexed | Performs complex multiplexing, as would be required in a large conference room (multiple participants). Currently only used by app_confbridge.  | 
| bridge_simple | Performs simple channel-to-channel bridging. |  
| bridge_softmix | Performs simple multiplexing, as would be required in a large conference room (multiple participants). Currently only used by app_confbridge. |  


# Call Detail Recording Modules  

> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

Call Detail Records (CDRs) will print basic call information to a file or a database. Note that this only captures some of the call info, though - you may want to utilize [channel event logging](telephony/asterisk/modules?id=channel-event-logging-modules), too.  


| Name | Description |  
| --- | --- |  
| cdr_adaptive_odbc | Allows writing of CDRs through ODBC framework with ability to add custom fields |  
| cdr_csv | Writes CDRs to disk as a comma-separated values (CSV) file |  
| cdr_custom | Writes CDRs to a CSV file, but allows addition of custom fields |  
| cdr_odbc | Writes CDRs through ODBC framework |  
| cdr_syslog | Writes CDRs to syslog |  

# Channel Event Logging Modules  

> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

<font color="green">Channel event logging</font> (<font color="green">CEL</font>) provides additional elements that can supplement [CDRs](telephony/terms?id=call-data-record-cdr) (which are also captured by Asterisk [if enabled](telephony/asterisk/modules?id=call-detail-recording-modules)).  


| Name | Description |  
| --- | --- |  
| cel_custom  | CEL to disk/file |  
| cel_manager | CEL to AMI |  
| cel_odbc | CEL to ODBC |  

!> Careful, apparently these are not simply plug and play - you will need to set up the dialplan correctly.   

# Channel Drivers  


> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

<font color="green">Channel Drivers</font> enable Asterisk to make and receive calls. The channel module acts as a gateway to the Asterisk core.  


| Name | Description |  
| --- | --- |  
| chan_bridge | Used internally by the ConfBridge() application; should not be used directly |  
| chan_dahdi | Provides connection to PSTN cards that use DAHDI channel drivers |  
| chan_local | Provides a mechanism to treat a portion of the dialplan as a channel |  
| chan_motif | Implements the Jingle protocol, including the ability to connect to Google Talk and Google Voice |  
| chan_multicast_rtp | Provides connection to multicast Realtime Transport Protocol (RTP) streams |  
| chan_pjsip | Session Initiation Protocol (SIP) channel driver |  


# Codec Translators  

> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

<font color="green">Codec Translators</font> (i.e. <font color="green">Transcoders</font>) allow Asterisk to convert audio stream formats on a channel. Its possible that each [channel](telephony/terms?id=channel) has its own codex; as an example, a SIP channel typically uses the codec called 'G.729', and a channel representing a PSTN line in the USA/Canada uses the μ-law (pronounced: mew-law) codec; if they were both speaking to each other, the audio would have to be converted from one to the other.  

| Name | Description |  
| --- | --- |  
| codec_alaw | A-law PCM codec used all over the world on the PSTN (except Canada/USA). This codec (along with ulaw) should be enabled on all your channels. |  
| codec_g729 | Was until recently a patented codec, but is now royalty-free. As of this writing it is still sold by Digium as an add-on, but it can also be found as a free package. It’s a very popular codec if compression is desired (and CPU use is not an issue), but it imposes load on the CPU, adds latency to calls, reduces quality slightly, and will not reduce overhead in any way. This is a decent choice of a codec if you need less bandwidth. |  
| codec_a_mu | A-law to mu-law (i.e. μ-law) direct converter. |  
| codec_g722 | Wideband audio codec. |  
| codec_gsm | Global System for Mobile Communications (GSM) codec. Very poor sound quality.
| codec_ilbc | Internet Low Bitrate Codec.
| codec_lpc10 | Linear Predictive Coding vocoder (extremely low bandwidth).
| codec_opus | Intended to replace speex (and vorbis).
| codec_resample | Resamples between 8-bit and 16-bit signed linear.
| codec_speex | Speex codec.
| codec_ulaw | Mu-law PCM codec used on PSTN in Canada/USA. Also known as g711, it’s more formally written as μ-law (and pronounced 'mew-law'), but not many people have a Greek letter μ on their keyboard, so it’s popularly written as ulaw. This is often the default codec, and should be enabled on all your channels. This is 56k, is uncompressed, and offers excellent quality. |  
* Usually, g711 and g729 are _the_ choices, due to quality.  


!> If at all possible, try to avoid transcoding - it is a heavy load on the CPU.  

# Format Interpreters  

> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

<font color="green">Format Interpreters</font> are similar to [codec translators](telephony/asterisk/modules?id=call-detail-recording-modules), but they work on files instead of channels (for example, system recordings). In addition, they can convert photos and video as well as audio. These are useful if you wish to play recordings that are stored in various formats.  

For most purposes, WAV files will be best - and will not need to be transcoded. However, if you have multiple files of different codecs, Asterisk will pick the file with the codec that best matches.  

| Name | Description |  
| --- | --- |   
| format_g729 | G.729: .g729. |  
| format_gsm | RPE-LTP (original GSM codec): .gsm |  
| format_h264 | H.264 video: .h264 |  
| format_ilbc | Internet Low Bitrate Codec: .ilbc |  
| format_jpeg | Graphic file: .jpeg, .jpg |  
| format_ogg_vorbis | Ogg container: .ogg |  
| format_pcm | Various Pulse-Coded Modulation formats: .alaw, .al, .alw, .pcm, .ulaw, .ul, .mu, .ulw, .g722, .au |  
| format_siren14 | G.722.1 Annex C (14 kHz): .siren14 |  
| format_siren7 | G.722.1 (7 kHz): .siren7 |  
| format_sln | 8-bit signed linear: .sln, .raw |  
| format_vox | .vox |  
| format_wav | .wav |  
| format_wav_gsm | GSM audio in a WAV container: .wav, .wav49 |  

!> If at all possible, try to avoid transcoding - it is a heavy load on the CPU.  

# Dialplan Functions  

> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

<font color="green">Dialplan Functions</font> are, effectively, functions / methods that can be called in the dialplan. They do stuff like format strings and dates, connect to databases, etc.  

| Name | Description |  
| --- | --- |  
| func_audiohook_inherit | Allows calls to be recorded after transfer |  
| func_blacklist | Writes/reads blacklist in astdb |  
| func_callcompletion | Gets/sets call-completion configuration parameters for the channel |  
| func_callerid | Gets/sets caller ID |  
| func_cdr | Gets/sets CDR variable |  
| func_channel | Gets/sets channel information |  
| func_config | Includes AST_CONFIG(); reads variables from config file |  
| func_curl | Uses cURL to obtain data from a URI |  
| func_cut | Slices and dices strings |  
| func_db | Provides astdb functions |  
| func_devstate | Gets state of device |  
| func_dialgroup | Creates a group for simultaneous dialing |  
| func_dialplan | Validates that designated target exists in dialplan |  
| func_env | Includes FILE(), STAT(), and ENV(); performs operating system actions |  
| func_global | Gets/sets global variables |  
| func_groupcount | Gets/sets channel count for members of a group |  
| func_hangupcause | Gets/sets hangupcause information from the channel |  
| func_logic | Includes ISNULL(), SET(), EXISTS(), IF(), IFTIME(), and IMPORT(); performs various logical functions |  
| func_math | Includes MATH(), INC(), and DEC(); performs mathematical functions |  
| func_odbc | Allows dialplan integration with ODBC resources |  
| func_rand | Returns a random number within a given range |  
| func_realtime | Performs lookups within the Asterisk Realtime Architecture (ARA) |  
| func_redirecting | Provides access to information about where this call was redirected from |  
| func_shell | Performs Linux shell operations and returns results |  
| func_sprintf | Performs string format functions similar to C function of same name |  
| func_srv | Performs SRV lookups in the dialplan |  
| func_strings | Includes over a dozen string manipulation functions |  
| func_timeout | Gets/sets timeouts on channel |  
| func_uri | Converts strings to URI-safe encoding |  
| func_vmcount | Returns count of messages in a voicemail folder for a particular user |  

# PBX Modules  

> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

<font color="green">PBX Modules</font> are modules that provide the user additional control and configuration options.  


| Name | Description |  
| --- | --- |  
| pbx_config | This module provides the traditional, and most popular, dialplan language for Asterisk. Without this module, Asterisk cannot read extensions.conf. |  
| pbx_dundi | Performs data lookups on remote Asterisk systems. |  
| pbx_realtime | Provides functionality related to the Asterisk Realtime Architecture. |  
| pbx_spool | Provides outgoing spool support relating to Asterisk call files. |  


# Resource Modules  

> From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/); note this is just a sampling, this is not an exhaustive list of modules.  

<font color="green">Resource Modules</font> integrate Asterisk with external resources.  


| Name | Type | Description |  
| --- | --- | --- |  
| res_config_curl | Configuration | Pulls configuration information using cURL |  
| res_config_ldap | Configuration | Pulls configuration information using LDAP |  
| res_config_odbc | Configuration | Pulls configuration information using ODBC |  
| res_calendar | Calendar | Enables base integration to calendaring systems |  
| res_calendar_caldav | Calendar | Allows features provided by res_calendar to connect to calendars via CalDAV |  
| res_calendar_exchange | Calendar | Allows features provided by res_calendar to connect to MS Exchange |  
| res_calendar_icalendar | Calendar | Allows features provided by res_calendar to connect to Apple/Google iCalendar |  
| res_adsi | Other | Provides ADSI (Used by the voicemail application, but not much else) |  
| res_agi | Other | Provides the Asterisk Gateway Interface  |  
| res_corosync | Other | Provides distributed message waiting indication (MWI) and device state notifications via the Corosync Cluster Engine |  
| res_crypto | Other | Provides cryptographic capabilities |  
| res_curl | Other | Provides common subroutines for other cURL modules |  
| res_fax | Other | Provides common subroutines for other fax modules |  
| res_fax_spandsp | Other | Plug-in for fax using the spandsp package |  
| res_http_post | Other | Provides POST upload support for the Asterisk HTTP server |  
| res_http_websocket | Other | Provides WebSocket support for the Asterisk internal HTTP server (required by WebRTC) |  
| res_monitor | Other | Provides call-recording resources |  
| res_musiconhold | Other | Provides music on hold (MOH) resources |  
| res_mutestream | Other | Allows muting/unmuting of audio streams |  
| res_odbc Provides | Other | common subroutines for other ODBC modules |  
| res_phoneprov | Other | Provisions phones from Asterisk HTTP server |  
| res_pktccops | Other | Provides PacketCable COPS resources |  
| res_security_log | Other | Enables logging of security events generated by other parts of Asterisk |  
| res_snmp | Other | Provides system status information to an SNMP-managed network |  
| res_speech | Other | Generic speech recognition API (requires a separate licensed product) |  
| res_stasis | Other | Ties together the various components of the Stasis application infrastructure |  
| res_xmpp | Other | Provides XMPP resources (FKA Jabber) |  
* For the 'configuration' modules, these are alternatives to the config files found in `/etc/asterisk`  
* The 'Calendar' modules allow you to connect to various calendar services.  