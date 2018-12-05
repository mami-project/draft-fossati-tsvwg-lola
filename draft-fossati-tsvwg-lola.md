---
title: A Loss-Latency Trade-off Signal for the Mobile Network
abbrev: LoLa Tradeoff
docname: draft-fossati-tsvwg-lola-latest
date:
category: std

ipr: trust200902
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
  -
    ins: T. Fossati
    name: Thomas Fossati
    org: Nokia
    email: thomas.fossati@nokia.com
  -
    ins: G. Fairhurst
    name: Gorry Fairhurst
    org: University of Aberdeen
    email: gorry@erg.abdn.ac.uk
  -
    ins: P. A. Aranda Gutierrez
    name: Pedro A. Aranda Gutierrez
    org: UC3M
    email: paranda@it.uc3m.es
  -
    ins: M. Kuehlewind
    name: Mirja Kuehlewind
    org: ETH Zurich
    email: mirja.kuehlewind@tik.ee.ethz.ch

normative:
    I-D.white-tsvwg-nqb:

informative:
    Podlesny:
      title: "Rd Network Services: Differentiation Through Performance Incentives"
      author:
        -
          ins: M. Podlesny
        -
          ins: S. Gorinsky
      date: 1900
    Custura:
      title: "Exploring DSCP modification pathologies in mobile edge networks"
      author:
        -
          ins: A. Custura
        -
          ins: A. Venne
        -
          ins: G. Fairhurst
      date: 1900
    Shbair:
      title: "A Multi-Level Framework to Identify HTTPS Services"
      author:
        -
          ins: W. M. Shbair
        -
          ins: T. Cholez
        -
          ins: J. Francois
        -
          ins: I. Chrisment
      date: 1900

--- abstract

todo

--- middle

# Introduction

Mobile networks are configured to actively remark or bleach DSCP coming from an
interconnect {{Custura}}.  Besides, they typically bundle all flows to and from
the Internet into a single default bearer, whose buffering characteristics are
not compatible with low-latency traffic.  The established behaviour is partly
rooted in the desire to prioritise operators' voice services over competing
over-the-top services, but that market consideration seems to have lost
relevance in the recent years.  It looks like the incentives are now aligned in
the direction of allowing more suitable treatment of Internet real-time flows.
However, a couple of preconditions need to be satisfied before we can move on
from the status quo.  First, the real-time flows must be efficiently identified
so that they can be put on the right queue especially nearby the bottleneck
link, which in 4G mobile networks is typically the air interface.  (This is at
odds with the rise of encrypted and multiplexed transports, which has the
potential of increasing the cost/accuracy ratio of DPI over the acceptable
threshold.)  And second, the signal must be such that the Mobile network can
trust it or, even better, avoid the need to trust it altogether.  This document
suggests reusing the Non Queue Building signalling protocol described in
{{I-D.white-tsvwg-nqb}} as the explicit signal that can be used by endpoints to
mark their real-time flows and the LTE network to use the marking to route them
through a suitable (low-latency) bearer through the LTE core and air interface.

# Terminology

- QCI: QoS Class Identifier
- TFT: Traffic Flow Template

{::boilerplate bcp14}

# DiffServ Code

Given the semantic equivalence of LoLa and the Non Queue Building (NQB) PHB,
this document reuses the NQB DSCP as-is.

# Marking

Endpoints SHOULD mark packets that are latency sensitive using the NQB DSCP.

Note that the marking could also be added just before the packet enters the LTE
core, for example by a classifier in the SGi-LAN.  This has the disadvantage of
working only for downlink.

# Mobile Network Behaviour

The Mobile network is configured to give UEs a dedicated low-latency EPS bearer
(suggested QCI 7) in addition to the default EPS bearer.

A packet carrying the NQB DSCP SHOULD be routed through the dedicated
low-latency EPS bearer.

# On Remarking and Bleaching

TODO consideration on DSCP remarking / bleaching observed at the interconnect
with mobile networks {{Custura}}.  This shouldn't be a problem for this use
case, but it's better not saying that...

# IANA Considerations

This document makes no request to IANA.

# Security Considerations

Internet applications cannot benefit from wrongly indicating low-latency as
they have to pay the expense of high loss as a tradeoff.  Hence there is no
incentive for Internet applications to set the wrong codepoint.

The signal is not integrity protected and could be flipped by an on-path
attacker.  This might negatively affect the QoS of the flow.

# Privacy Considerations

As described in {{Shbair}} state of art encrypted traffic analysis based
machine learning can successfully identify the type of transported application
(e.g., HTTPS, SMTP, P2P, VoIP, SSH, Skype) with good accuracy and without any
need to access the clear-text.  In this context, and despite its limitations
(i.e., fuzzy, coarse grained), a 1-bit signal such as the one described in this
document might be used to improve the precision of the classifier. 

# Acknowledgments

The authors of Latency Loss Tradeoff PHB Group draft: Jianjie You, Michael
Welzl, Brian Trammell, Mirja Kuehlewind and Kevin Smith.
Dan Druta, Diego Lopez, Shamit Bhat, Chris Seal, Georg Mayer, Florin Baboescu,
James Gruessing.

This work is partially supported by the European Commission under Horizon 2020
grant agreement no. 688421 Measurement and Architecture for a Middleboxed
Internet (MAMI), and by the Swiss State Secretariat for Education, Research,
and Innovation under contract no. 15.0268.  This support does not imply
endorsement. 
