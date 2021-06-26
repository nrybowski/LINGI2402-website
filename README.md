# LINGI2402: Open-Source Project

## QLOG extension for (MP)TCP

### QLOG

[QLOG](https://quicwg.org/qlog/draft-ietf-quic-qlog-main-schema.html) is a new general purpose logging format for network protocols.
It is an endpoint logging format.
That is, it logs events from within implementations.
Its main usages are:
- the visualization of network traces under different forms (See [QVIS](https://github.com/quiclog/qvis)). 
- the creation of debugging tools.

At this time, specific extensions exist for:
- [QUIC](https://quicwg.org/qlog/draft-ietf-quic-qlog-quic-events.html) events
- [HTTP3](https://quicwg.org/qlog/draft-ietf-quic-qlog-h3-events.html) events

This project is aimed to propose the basis of such extension for the (MP)TCP protocols.

### Contributions

This page is a summary of the contributions.
A detailed (technical) documentation is available in the [project repository](https://github.com/nrybowski/quicSim-docker/tree/master/tcpqlog).

#### Prototype logger

A prototype logger is implemented in Python.
It leverages `eBPF` and kernel probes (kprobes) tracing in the `MPTCPv1` Linux kernel implementation.

`eBPF` programs are attached to kprobes (with `BCC`) and are triggered upon the probed function execution.
They collect some data and send them back to the user-space through perf buffers.
The user-space utility then formats the events according to the proposed QLOG format and dumps them in files with the JSON format.

#### Format sample

Here is a sample of the current client-side view of the 3-way handshakes form a 3-subflows MPTCP connection.

```
[13237874729, 'connectivity', 'connection_started', {'dst_ip': '172.17.0.10', 'dst_port': 8000, 'ip_version': '4', 'src_ip': '172.17.0.30', 'src_port': 50952, 'transport_protocol': 'TCP'}, '2ba0ebea0ce9f6b9f6d1a6098cbeaa9e']
[13237874729, 'transport', 'packet_sent', {'header': {'flags': ['SYN'], 'seq': 182139483, 'win': 0}}, '2ba0ebea0ce9f6b9f6d1a6098cbeaa9e']
[13238791973, 'transport', 'packet_received', {'header': {'flags': ['SYN', 'ACK'], 'seq': 2079434258, 'ack': 182139484, 'win': 65160}}, '2ba0ebea0ce9f6b9f6d1a6098cbeaa9e']
[13238839345, 'transport', 'packet_sent', {'header': {'flags': ['ACK'], 'ack': 1, 'win': 0}}, '2ba0ebea0ce9f6b9f6d1a6098cbeaa9e']

[...]

[13241118215, 'connectivity', 'subflow_connection_started', {'dst_ip': '172.17.0.10', 'dst_port': 8000, 'ip_version': '4', 'src_ip': '172.17.1.31', 'src_port': 48615, 'transport_protocol': 'TCP'}, 'cdbdf00b802e84969f4dfcce3c43f37e', '2ba0ebea0ce9f6b9f6d1a6098cbeaa9e']
[13241118215, 'transport', 'packet_sent', {'header': {'flags': ['SYN'], 'seq': 1873275315, 'win': 0}}, 'cdbdf00b802e84969f4dfcce3c43f37e']
[13243053215, 'transport', 'packet_received', {'header': {'flags': ['SYN', 'ACK'], 'seq': 852837744, 'ack': 1873275316, 'win': 65160}}, 'cdbdf00b802e84969f4dfcce3c43f37e']
[13243088040, 'transport', 'packet_sent', {'header': {'flags': ['ACK'], 'ack': 1, 'win': 0}}, 'cdbdf00b802e84969f4dfcce3c43f37e']

[...]

[13243425262, 'connectivity', 'subflow_connection_started', {'dst_ip': '172.17.0.10', 'dst_port': 8000, 'ip_version': '4', 'src_ip': '172.17.2.32', 'src_port': 55137, 'transport_protocol': 'TCP'}, 'a90078718fcc22e98d79a3c56cb69d65', '2ba0ebea0ce9f6b9f6d1a6098cbeaa9e']
[13243425262, 'transport', 'packet_sent', {'header': {'flags': ['SYN'], 'seq': 863497603, 'win': 0}}, 'a90078718fcc22e98d79a3c56cb69d65']
[13244046390, 'transport', 'packet_received', {'header': {'flags': ['SYN', 'ACK'], 'seq': 1201300528, 'ack': 863497604, 'win': 65160}}, 'a90078718fcc22e98d79a3c56cb69d65']
[13244075302, 'transport', 'packet_sent', {'header': {'flags': ['ACK'], 'ack': 1, 'win': 0}}, 'a90078718fcc22e98d79a3c56cb69d65']
```

### Tools

Here is the list of all the (open-source) tools used in this project:
- [`MPTCPv1` Linux kernel implementation](https://github.com/multipath-tcp/mptcp_net-next)
- [BCC](https://github.com/iovisor/bcc): Python library which allows attaching `eBPF` programs to various hooks.
- [virtme](https://github.com/amluto/virtme): Simple tool to run virtualized Linux kernels.
- [mptcp-tools](https://github.com/pabeni/mptcp-tools): Simple utility forcing applications to use MPTCP rather than TCP.
- [docker](https://www.docker.com/): Container runtime.
