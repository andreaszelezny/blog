---
title: AI Planning for Cyber Security
author: Andy
type: post
showtoc: false
date: 2015-06-22T23:27:06+00:00
url: /post/ai-planning-for-cyber-security/
categories:
  - Artificial Intelligence
  - Information Security
tags:
  - ai
  - cyber security
  - planning

---
A very interesting application of classical Artificial Intelligence (AI) planning in the field of cyber security is the Behavioral Adversary Modeling System (BAMS) developed by Mark Boddy, Johnathan Gohde, Tom Haigh and Steven Harp. 

The domain is computer network vulnerability analysis and the purpose of the system is to automatically generate an inside attacker&rsquo;s course of action leading from various initial states to an attacker&rsquo;s goal. The users of the system (i.e., computer network administrators) can use these attack plans to analyze vulnerabilities and to select the most reasonable and cost-effective defensive measures for their networks.

The attack plans contain 20 to 50 atomic steps each and are generated within seconds. The plan generator is a classical forward heuristic planner, in this specific case the Metric-FF planner which offers two different search modes, enhanced hill climbing and best first search.

The planning domain and the planning problem are both respresented in PDDL (Planning Domain Definition Language). The model contains many objects (e.g., computers, switches, programs, files, cryptographic keys, people, doors) and predicates (e.g., configuration of hosts, knowledge of people, status of files) to represent a computational environment. Actions change the computational environment and allow the attacker to reach her goal. Here is an example of an action which modifies the access control list for a document by giving read access for a group:

<pre>(:action DMS_ADD_GROUP_ALLOW
      :parameters ( ?admin - c_human
        ?chost - c_host
        ?shost - c_host
        ?doc - c_file
        ?gid - c_gid )
      :precondition
        (and (pmode free)
        (nes_admin_connected ?chost ?shost)
        (at_host ?admin ?chost)
        (insider ?admin))
      :effect
        (and
        (dmsacl_read ?doc ?gid)))
</pre>

The goal in this example is that Bob gets to know some secret information while keeping his detection risk low:

<pre>(:goal (knows bob secret_info))
    (:metric minimize (detection_risk))
</pre>

The PDDL domain and problem specifications are given as input to the planner, which automatically generates an attacking plan like the following:

<pre>0 : ADAM sits down at BIGFOOT
    1 : ADAM enters ADAM_UID as user name for login on host BIGFOOT
    2 : ADAM enters password ADAM_PWD for login at host BIGFOOT
    3 : Shell B_WEXPLORE is launched on host BIGFOOT for user ADAM_UID
    4 : Program WEXPLORER on host BIGFOOT forks a child process
    5 : Contents of file B_IEXPLORE begin executing as uid ADAM_UID on host BIGFOOT
    6 : BOB sits down at YETI
    7 : BOB enters BOB_UID as user name for login on host YETI
    8 : BOB enters password BOB_PWD for login at host YETI
    9 : Shell Y_WEXPLORE is launched on host YETI for user BOB_UID
    10 : Program WEXPLORER on host YETI forks a child process
    11 : Contents of file Y_ETHEREAL begin executing as uid BOB_UID on host YETI
    12 : ETHEREAL starts sniffing the networks on YETI
    13 : ADAM logs onto dms admin server EVEREST from BIGFOOT
    14 : BOB reads the sniffer thus learning NES_ADMIN_PASS
    15 : Program WEXPLORER on host YETI forks a child process
    16 : Contents of file Y_IEXPLORE begin executing as uid BOB_UID on host YETI
    17 : BOB logs onto dms admin server EVEREST from YETI
    18 : DMS session DMSS1 has begun
    19 : BOB begins a DMS session on YETI
    20 : Connect DMS session DMSS1 to server NES on EVEREST
    21 : A route from YETI to DMS server EVEREST exists
    22 : BOB enters password BOB_DMS_PWD for the DMS session.
    23 : Authenticate BOB_UID in dms session DMSS1 with EVEREST using BOB_DMS_PWD
    24 : BOB adds an acl to allow read access of E_SECRET_DOC to the EAST_GID group
    25 : BOB begins a DMS request at YETI in session DMSS1
    26 : Document E_SECRET_DOC is requested in session DMSS1
    27 : Document E_SECRET_DOC is sent and displayed on YETI in session DMSS1
    28 : BOB reads E_SECRET_DOC and learns SECRET_INFO
</pre>

Even though the number of objects, propositions and actions is very limited in this system, it is still a very interesting proof of concept for the usage of artificial intelligence planning in the information security domain. Future work could also include a notion of uncertainty in the plan (i.e., probabilistic planning).

**References**  
Boddy, M.; Gohde, J.; Haigh, T.; and Harp, S.: Course of Action Generation for Cyber Security Using Classical Planning. In: Proceedings of ICAPS, 2005.