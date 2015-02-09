# Troubleshooting

You can ask questions per mail.

### Error Reporting

The application does not proper report errors. This needs to be done in an update coming soon.


### IPTables rules

Whatsapp uses an HTTPS connection which is going outbound:

`iptables -A OUTPUT -o eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT`

`iptables -A INPUT -i eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT`