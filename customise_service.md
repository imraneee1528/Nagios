## Client Server 
<pre>
  cd  /usr/lib/nagios/plugins/
</pre>
<pre>
vim check_gp_error 
</pre> 
<pre>
#!/usr/bin/env bash
# Date: 2023-04-14

# Nagios Exit Codes
OK=0
WARNING=1
CRITICAL=2
UNKNOWN=3
EXIT_CODE=$?

duration=1
minutestamp=$(date -d "$duration minutes ago" '+%d/%b/%Y:%H:%M')
GP_5XX=$(cat /usr/local/src/nagios/access.log | grep $minutestamp | egrep "/gp HTTP/1.1\" 50" | wc -l) 
GP_4XX=$(cat /usr/local/src/nagios/access.log | grep $minutestamp | egrep "/gp HTTP/1.1\" 40" | wc -l) 

CODE=""

if [ "$GP_5XX" -eq "0" ] && [ "$GP_4XX" -eq "0" ]; then
	CODE="NULL"
elif [ "$GP_5XX" -ne "0" ] && [ "$GP_4XX" -ne "0" ]; then
	CODE="5XX4XX"
elif [ "$GP_5XX" -ne "0" ]; then
	CODE="5XX"
elif [ "$GP_4XX" -ne "0" ]; then
        CODE="4XX"
else
	CODE="UNKNOWN"
fi

case $CODE in
"NULL")
	echo "[GrameenPhone-]OK - CODE 5XX: $GP_5XX; CODE 4XX: $GP_4XX;"
        exit $OK
        ;;
"5XX4XX")
        echo "[GrameenPhone-]CRITICAL - CODE 5XX: $GP_5XX; CODE 4XX: $GP_4XX;"
        exit $CRITICAL
        ;;
"5XX")
        echo "[GrameenPhone-]CRITICAL - CODE 5XX: $GP_5XX;"
        exit $CRITICAL
        ;;
"4XX")
        echo "[GrameenPhone-]CRITICAL - CODE 4XX: $GP_4XX;"
        exit $CRITICAL
        ;;
"UNKNOWN")
        echo "Is there a typo in the command or service configuration?"
        exit $UNKNOWN
        ;;
*)
        echo "Is there a typo in the command or service configuration?"
        exit $UNKNOWN
        ;;
esac
</pre>
<pre>
vim /etc/nagios/nrpe.cfg
command[check_response_time]=/usr/lib/nagios/plugins/check_response_time
</pre>
<pre>
/usr/lib/nagios/plugins/check_response_time
 systemctl restart nagios-nrpe-server.service
</pre>

# Server end 
<pre>
 vim proda2putilityserver1.cfg
</pre>

<pre>
define service{
        use                     generic-service
        host_name               proda2putilityserver1
        service_description     Response_Time
        check_command           check_response_time_proda2putilityserver1
        check_interval          5
        retry_interval          1
        max_check_attempts      1
        check_period            24x7
        flap_detection_enabled  0
        notifications_enabled   1
        contact_groups          mnpspdc_alert
}
</pre>
<pre>
	define command{
        command_name    check_response_time_proda2putilityserver1
        command_line    $USER1$/check_nrpe -2 -H 192.168.200.101 -c check_response_time
}
</pre>
<pre>
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
 systemctl reload nagios.service
</pre>
