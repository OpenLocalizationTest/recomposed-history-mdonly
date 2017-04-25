### <a name="noconnection"></a>To modify IP address prefixes - no gateway connection

If you don't have a gateway connection and you want to add or remove the IP address prefixes contained in your local network gateway, you use the same command that you use to create the local network gateway. You can also use this command to update the gateway IP address for the VPN device. 

Only use this command when you do not already have a connection. Be sure to use the existing name of your local network gateway in order to overwrite the current settings. If you use a different name, you create a new local network gateway, instead of overwriting the existing one.

Each time you make a change, the entire list of prefixes must be specified, not just the prefixes that you want to change. Specify only the prefixes that you want to keep. In this case, 10.0.0.0/24 and 20.0.0.0/24


```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>To modify IP address prefixes - existing gateway connection

If you have a gateway connection and want to add or remove the IP address prefixes contained in your local network gateway, you can update the local network gateway. This results in some downtime for your VPN connection. When modifying the IP address prefixes, you don't need to delete the VPN gateway.

Each time you make a change, the entire list of prefixes must be specified, not just the prefixes that you want to change. In this example, 10.0.0.0/24 and 20.0.0.0/24 are already present. We add the prefixes 30.0.0.0/24 and 40.0.0.0/24 and specify all 4 of the prefixes when updating.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
```