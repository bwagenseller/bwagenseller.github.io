# Misc Telephony Tools  

# sngrep  

<font color="green">sngrep</font> is a SIP packet-sniffing tool that can filter on the keywords you pass in.  

## sngrep Install  

To install <font color="green">sngrep</font>, [become root](/operating_systems/ubuntu/linux_notes?id=becoming-root), [update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages), and then run: `apt-get install sngrep`

## Capturing SIP Packets with sngrep  

!> You must [become root](/operating_systems/ubuntu/linux_notes?id=becoming-root) in order to run <font color="green">sngrep</font>!  

The general usage is:  
```
sngrep [PHRASE_HERE]
```  
* Replace `[PHRASE_HERE]` with what you wish to filter.  
   * Often this is a ten-digit phone number (without dashes or parenthesis), but it can be anything that will be displayed in the SIP message.  

