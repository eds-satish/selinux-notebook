# Network Labeling Statements

The network labeling statements are used to label the following objects:

**Network interfaces** - This covers those interfaces managed by the
***ifconfig**(8)* command.

**Network nodes** - These are generally used to specify host systems
using either IPv4 or IPv6 addresses.

**Network ports** - These can be either udp, tcp, dccp or sctp port numbers.

A security context is defined by these network labeling statements,
therefore if the policy supports MCS / MLS, then an `mls_range` is
required as described in the
[**MLS Statements - MLS range Definition**](mls_statements.md#mls-range-definition)
section. Note that there are no terminating semi-colons ';' on these statements.

If any of the network objects do not have a specific security context
assigned by the policy, then the value given in the policies initial SID
is used (`netif`, `node` or `port` respectively), as shown below:

```
# Network Initial SIDs from the MLS Reference Policy:

sid netif system_u:object_r:netif_t:s0 - s15:c0.c255
sid node system_u:object_r:node_t:s0 - s15:c0.c255
sid port system_u:object_r:port_t:s0
```

### IPv4 Address Format

IPv4 addresses are represented in dotted-decimal notation (four
numbers, each ranging from 0 to 255, separated by dots as shown:

`192.77.188.166`

### IPv6 Address Formats

IPv6 addresses are written as eight groups of four hexadecimal digits,
where each group is separated by a colon ':' as follows:

`2001:0db8:85a3:0000:0000:8a2e:0370:7334`

To shorten the writing and presentation of addresses, the following
rules apply:

1.  Any leading zeros in a group may be replaced with a single '0' as
    shown:

`2001:db8:85a3:0:0:8a2e:370:7334`

2.  Any leading zeros in a group may be omitted and be replaced with two
    colons '::', however this is only allowed once in an address as
    follows:

`2001:db8:85a3::8a2e:370:7334`

3.  The *localhost* (loopback) address can be written as:

`0000:0000:0000:0000:0000:0000:0000:0001`

Or

`::1`

4.  An undetermined IPv6 address i.e. all bits are zero is written as:

`::`

<br>

## `netifcon`

The `netifcon` statement is used to label network interface objects (e.g.
eth0) for peer labeling (see the
[**`netif` object class**](object_classes_permissions.md#network-object-classes)).

It is also possible to use the ***semanage**(8)* interface command to associate
the interface to a security context.

**The statement definition is:**

`netifcon netif_id netif_context packet_context`

**Where:**

<table>
<tbody>
<tr>
<td><code>netifcon</code></td>
<td>The <code>netifcon</code> keyword.</td>
</tr>
<tr>
<td><code>netif_id</code></td>
<td>The network interface name (e.g. eth0).</td>
</tr>
<tr>
<td><code>netif_context</code></td>
<td>The security context allocated to the network interface.</td>
</tr>
<tr>
<td><code>packet_context</code></td>
<td><p>The security context allocated packets. Note that these are defined but unused.</p>
<p>The iptables(8)/nftables(8) <a href="network_support.md#secmark">SECMARK services</a> should be used to label packets.</p></td>
</tr>
</tbody>
</table>

**The statement is valid in:**

<table style="text-align:center">
<tbody>
<tr style="background-color:#D3D3D3;">
<td><strong>Monolithic Policy</strong></td>
<td><strong>Base Policy</strong></td>
<td><strong>Module Policy</strong></td>
</tr>
<tr>
<td>Yes</td>
<td>Yes</td>
<td>No</td>
</tr>
<tr style="background-color:#D3D3D3;">
<td><strong>Conditional Policy <code>if</code> Statement</strong></td>
<td><strong><code>optional</code> Statement</strong></td>
<td><strong><code>require</code> Statement</strong></td>
</tr>
<tr>
<td>No</td>
<td>No</td>
<td>No</td>
</tr>
</tbody>
</table>

**Example:**

```
# The following netifcon statement has been taken from the
# MLS policy that shows an interface name of lo with the same
# security context assigned to both the interface and packets.

netifcon lo system_u:object_r:lo_netif_t:s0 - s15:c0.c255 system_u:object_r:unlabeled_t:s0 - s15:c0.c255
```

***semanage**(8)* **Command example:**

`semanage interface -a -t netif_t eth2`

This command will produce the following file in the default
&lt;SELINUXTYPE&gt; policy store and then activate the policy:

*/var/lib/selinux/&lt;SELINUXTYPE&gt;/active/interfaces.local*:

```
# This file is auto-generated by libsemanage
# Do not edit directly.

netifcon eth2 system_u:object_r:netif_t:s0
system_u:object_r:netif_t:s0
```

<br>

## `nodecon`

The `nodecon` statement is used to label network address objects for peer
labeling (see the
[**`node` object class**](object_classes_permissions.md#network-object-classes)
that represent IPv4 or IPv6 IP addresses and network masks.

It is also possible to add SELinux these outside the policy using the
***semanage**(8)* 'node' command that will associate the node to a security
context.

**The statement definition is:**

`nodecon subnet netmask node_context`

**Where:**

<table>
<tbody>
<tr>
<td><code>nodecon</code></td>
<td>The <code>nodecon</code> keyword.</td>
</tr>
<tr>
<td><code>subnet</code></td>
<td><p>The subnet or specific IP address in IPv4 or IPv6 format.</p>
<p>Note that the subnet and netmask values are used to ensure that the node_context is assigned to all IP addresses within the subnet range.</p></td>
</tr>
<tr>
<td><code>netmask</code></td>
<td>The subnet mask in IPv4 or IPv6 format.</td>
</tr>
<tr>
<td><code>node_context<code></td>
<td>The security context for the node.</td>
</tr>
</tbody>
</table>

**The statement is valid in:**

<table style="text-align:center">
<tbody>
<tr style="background-color:#D3D3D3;">
<td><strong>Monolithic Policy</strong></td>
<td><strong>Base Policy</strong></td>
<td><strong>Module Policy</strong></td>
</tr>
<tr>
<td>Yes</td>
<td>Yes</td>
<td>No</td>
</tr>
<tr style="background-color:#D3D3D3;">
<td><strong>Conditional Policy <code>if</code> Statement</strong></td>
<td><strong><code>optional</code> Statement</strong></td>
<td><strong><code>require</code> Statement</strong></td>
</tr>
<tr>
<td>No</td>
<td>No</td>
<td>No</td>
</tr>
</tbody>
</table>

**Examples:**

```
# The MLS policy nodecon statement using an IPv4 address:

nodecon 127.0.0.1 255.255.255.255 system_u:object_r:lo_node_t:s0 - s15:c0.c255
```

```
# The MLS policy nodecon statement for the multicast address
# using an IPv6 address:

nodecon ff00:: ff00:: system_u:object_r:multicast_node_t:s0 - s15:c0.c255
```

***semanage**(8)* **Command example:**

`semanage node -a -t node_t -p ipv4 -M 255.255.255.255 127.0.0.2`

This command will produce the following file in the default
&lt;SELINUXTYPE&gt; policy store and then activate the policy:

*/var/lib/selinux/&lt;SELINUXTYPE&gt;/active/nodes.local*:

```
# This file is auto-generated by libsemanage
# Do not edit directly.

nodecon ipv4 127.0.0.2 255.255.255.255 system_u:object_r:node_t:s0
```

<br>

## `portcon`

The `portcon` statement is used to label udp, tcp, dccp or sctp ports.

It is also possible to add a security context to ports outside the
policy using the ***semanage**(8)* 'port' command that will associate the port
(or range of ports) to a security context.

**The statement definition is:**

`portcon protocol port_number port_context`

**Where:**

<table>
<tbody>
<tr>
<td><code>portcon</code></td>
<td>The <code>portcon</code> keyword.</td>
</tr>
<tr>
<td><code>protocol</code></td>
<td>The protocol type. Valid entries are udp, tcp or <em>dccp</em>.</td>
</tr>
<tr>
<td><code>port_number</code></td>
<td>The port number or range of ports. The ranges are separated by a hyphen (-).</td>
</tr>
<tr>
<td><code>port_context</code></td>
<td>The security context for the port or range of ports.</td>
</tr>
</tbody>
</table>

**The statement is valid in:**

<table style="text-align:center">
<tbody>
<tr style="background-color:#D3D3D3;">
<td><strong>Monolithic Policy</strong></td>
<td><strong>Base Policy</strong></td>
<td><strong>Module Policy</strong></td>
</tr>
<tr>
<td>Yes</td>
<td>Yes</td>
<td>No</td>
</tr>
<tr style="background-color:#D3D3D3;">
<td><strong>Conditional Policy <code>if</code> Statement</strong></td>
<td><strong><code>optional</code> Statement</strong></td>
<td><strong><code>require</code> Statement</strong></td>
</tr>
<tr>
<td>No</td>
<td>No</td>
<td>No</td>
</tr>
</tbody>
</table>

**Examples:**

```
# The MLS policy portcon statements:
portcon tcp 20 system_u:object_r:ftp_data_port_t:s0
portcon tcp 21 system_u:object_r:ftp_port_t:s0
portcon tcp 600-1023 system_u:object_r:hi_reserved_port_t:s0
portcon udp 600-1023 system_u:object_r:hi_reserved_port_t:s0
portcon tcp 1-599 system_u:object_r:reserved_port_t:s0
portcon udp 1-599 system_u:object_r:reserved_port_t:s0
```

***semanage**(8)* **Command example:**

`semanage port -a -t reserved_port_t -p udp 1234`

This command will produce the following file in the default
&lt;SELINUXTYPE&gt; policy store and then activate the policy:

*/var/lib/selinux/&lt;SELINUXTYPE&gt;/active/ports.local*:

```
# This file is auto-generated by libsemanage
# Do not edit directly.

portcon udp 1234 system_u:object_r:reserved_port_t:s0
```

<br>

<!-- %CUTHERE% -->

<table>
<tbody>
<td><center>
<p><a href="file-labeling-statements.md#file-system-labeling-statements" title="File System Labeling Statements"> <strong>Previous</strong></a></p>
</center></td>
<td><center>
<p><a href="README.md#the-selinux-notebook" title="The SELinux Notebook"> <strong>Home</strong></a></p>
</center></td>
<td><center>
<p><a href="infiniband_statements.md#infiniband-labeling-statements" title="InfiniBand Labeling Statements"> <strong>Next</strong></a></p>
</center></td>
</tbody>
</table>

<head>
    <style>table { border-collapse: collapse; }
    table, td, th { border: 1px solid black; }
    </style>
</head>