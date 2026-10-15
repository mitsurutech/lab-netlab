# Networking labs in one VM: static routing, OSPF and BGP with FRRouting

[![check](https://github.com/mitsurutech/lab-netlab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-netlab/actions/workflows/check.yml)

Three ready-made **computer networking labs** for classrooms, from the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning), where schools and colleges give every student their own machines and terminal in the browser. Each student gets their own Ubuntu VM with a small network built inside it, **routers running FRRouting** and hosts to ping from, and learns **static routing, OSPF and BGP** on the same `vtysh` console they will meet on real equipment. Nothing is installed on the student's computer and no Cisco licence is needed.

A router needs a machine of its own and a student has one VM, so every router and every host is a **network namespace**, every link a pair of virtual cables, and every router runs its own FRRouting daemons. The real commands work, which is the point: `ip route`, `traceroute -n`, `tcpdump`, `vtysh`.

## The three labs

| Lab | File | What the student does | Help page |
| --- | --- | --- | --- |
| Static routing | [`static-routing.lab.yaml`](static-routing.lab.yaml) | Find where a packet stops, add static routes on the routers that need them, keep a management network unreachable | [Static routing lab](https://mitsurutech.co.uk/help/lab-netlab-static) |
| OSPF | [`ospf.lab.yaml`](ospf.lab.yaml) | Bring up OSPF in area 0 on three routers, watch the hellos, wait for every adjacency to be Full, keep a network out of the advertisements | [OSPF lab](https://mitsurutech.co.uk/help/lab-netlab-ospf) |
| BGP | [`bgp.lab.yaml`](bgp.lab.yaml) | Make two autonomous systems neighbours, announce each one's network, and announce nothing of the private network behind one of them | [BGP lab](https://mitsurutech.co.uk/help/lab-netlab-bgp) |

Each lab leaves a README in the student's home folder that says what to do and gives none of the answers. Each has a **check**, a script that marks a handed-in VM without the teacher reading it: it runs on a copy of the saved machine after a fresh start, so it sees only what the student saved with `write memory`, and it passes only if the real state holds (the routes are in the routing tables, the OSPF neighbours are Full or the BGP session is Established, `h1` reaches `h2` along the path through the routers, and the private network stays unreachable).

## What is in a file

Each `.lab.yaml` is a whole **VM lab definition**, exactly what the platform runs:

| Key | What it is |
| --- | --- |
| `cpus`, `memory`, `disk` | the size of each student's VM: 2 CPUs, 2048 MB and 10 GB |
| `open` | the ports that open in the browser: none, the lab is the terminal |
| `setup` | a script run once, as root, the first time each student's VM starts: it installs FRRouting and `tcpdump` from Ubuntu's archive in about ten seconds, writes the `netlab` command, the topology and the lab's README |
| `check` | the marking script, run as root on a copy of the saved disk after a fresh boot; its exit status is the result and `0` passes |

The `netlab` command it installs: `netlab status` lists the machines, `netlab shell r1` opens a router's console and `netlab shell h1` a host, and `netlab reset` starts the lab again.

## Using it on the platform

**To set one as work for a class:**

1. In your module, add a task and choose the **Ubuntu VM lab** card.
2. Press the sample you want to start from, or paste the contents of one of the files here. Using a sample replaces the text, and Undo brings yours back.
3. Publish and try your own copy first. Every student gets a VM of their own, kept between sessions.
4. Assign it like any task. When it is handed in, run the check on a student's row in the class list: it starts a copy of their saved machine that is not saved back, and shows whether it passed and what it printed.

The help manual's [guide to Ubuntu VM labs](https://mitsurutech.co.uk/help/labs) and its [sample labs page](https://mitsurutech.co.uk/help/sample-labs) cover the rest. To make your own topology, copy a file and change the topology in its `setup`: one line for each router, host and link, and `daemons ospfd` or `daemons bgpd` to start that protocol on every router.

No college account yet? [Your first year is free](https://mitsurutech.co.uk/pricing) for up to 50 students, and [signing up](https://mitsurutech.co.uk/signup) takes an email address.

## Checking the files

On every push, `.github/workflows/check.yml` checks that each file is valid YAML with the five keys above and that `setup` and `check` are each inside the platform's 16 KB limit. It does not boot a VM: that is done on the platform's own machines.

## What does not work in a lab

The VM reaches Ubuntu's package archive, PyPI, npm and GitHub, and nothing else, so a lab cannot be pointed at a host on the internet. It is one VM, so there is no second machine to attack from or to; a topology of several routers is built inside it.

## About

Made by [Mitsuru Technologies](https://mitsurutech.co.uk) for the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning): Python, Java, Go, Node, SQL and Linux in the browser for schools and colleges, with container labs, VM labs, web apps deployed from GitHub, and quizzes that mark themselves. Try code with no account in the [playground](https://mitsurutech.co.uk/playground).

More labs from the same place: [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) and [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licence

The lab definitions, the `netlab` tool and this documentation are MIT licensed, see [LICENSE](LICENSE): copy them, change them, teach with them. [FRRouting](https://frrouting.org) is GPL-2.0 licensed and is installed from Ubuntu's archive when a VM starts; nothing of it is distributed here.
