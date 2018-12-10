---
title: A Loss-Latency Trade-off Signal for the Mobile Network
abbrev: Loss-Latency Tradeoff
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
    org: Universidad Carlos III de Madrid
    email: paranda@it.uc3m.es
  -
    ins: M. Kuehlewind
    name: Mirja Kuehlewind
    org: ETH Zurich
    email: mirja.kuehlewind@tik.ee.ethz.ch

normative:
    I-D.white-tsvwg-nqb:

informative:
    I-D.you-tsvwg-latency-loss-tradeoff:
    Podlesny:
      title: "Rd Network Services: Differentiation Through Performance Incentives"
      author:
        -
          ins: M. Podlesny
        -
          ins: S. Gorinsky
      seriesinfo: SIGCOMM
      date: 2008
    Custura:
      title: "Exploring DSCP modification pathologies in mobile edge networks"
      author:
        -
          ins: A. Custura
        -
          ins: A. Venne
        -
          ins: G. Fairhurst
      seriesinfo: TMA
      date: 2017
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
      seriesinfo: NOMS
      date: 2016
    Fossati:
      title: "Loss Latency Tradeoff and the Mobile Network"
      target: "https://github.com/mami-project/roadshows/blob/master/ietf-103-lola/hotrfc/build/main.pdf"
      author:
        -
          ins: T. Fossati
        -
          ins: P. A. Aranda Gutierrez
        -
          ins: M. Kuehlewind
        -
          ins: D. R. Lopez
      date: 2018

--- abstract

This document proposes a marking scheme for tagging low-latency flows (for
example: interactive voice and video, gaming, machine to machine applications)
so that they can be given appropriate treatment by the mobile network.  We
suggest that the mobile network uses such marking to route matching packets
through a dedicated, non-GBR, low-latency bearer - e.g., one with QCI 7 -
instead of the default EPS bearer.  The suggested scheme re-uses NQB, a
DiffServ-based signalling scheme with compatible semantics that has been
recently introduced in the context of fixed access to allow differential
treatment of non-queue building vs queue building flows.

--- middle

# Introduction

Today's mobile networks are configured to bundle all flows to and from the
Internet into a single "default" EPS bearer whose buffering characteristics are
not compatible with low-latency traffic.  The established behaviour is partly
rooted in the desire to prioritise operators' voice services over competing
over-the-top services.  Of late, said business consideration seems to have lost
momentum and the incentives might now be aligned towards allowing a more
suitable treatment of Internet real-time flows.  However, a couple of
preconditions need to be satisfied before we can move on from the status quo.
First, the real-time flows must be efficiently identified so that they can be
quickly assigned to the "right" EPS bearer.  This is especially important with
the rising popularity of encrypted and multiplexed transports, which has the
potential of increasing the cost/accuracy ratio of DPI-based classification
over the acceptable threshold.  Second, the signal must be such that malicious
or badly configured nodes can't abuse it.  Today's mobile networks take a
rather extreme posture in this respect by actively discarding (remarking or
bleaching {{Custura}}) DiffServ signalling coming from an interconnect.
Therefore, the signal must be modelled in a way that the mobile network can
either trust it or, even better, avoid the need of trusting it in the first
place.  The Rate-Delay trade-off {{Podlesny}} satisfies the above requirements
and has been shown {{Fossati}} to integrate well with a simplified LTE QoS
setup that uses one dedicated low-latency bearer in addition to the default.

This document suggests reusing the Non Queue Building (NQB) signalling
protocol described in {{I-D.white-tsvwg-nqb}} as the method employed by
endpoints to mark their real-time flows and by the LTE network to classify and
route these flows via a suitable (low-latency) bearer through the LTE core
network and E-UTRAN.

# Terminology

- EPS bearer: Evolved Packet System bearer, a virtual circuit with a given set
  of QoS attributes which spans the entire mobile network including the LTE
  core and E-UTRAN segments;
- GBR: Guaranteed Bit Rate.  EPS bearers can be GBR, in which case they are
  guaranteed to not loose packets under congestion, or non-GBR, in which case
  no guarantee of delivery is made by the mobile network;
- LTE: 3GPP Long Term Evolution, aka 4G;
- E-UTRAN: LTE Radio Access Network;
- QCI: QoS Class Identifier.  In LTE networks, EPS bearers are partitioned
  into equivalency classes modulo the QoS treatment they receive.  QCI is an
  integer that labels a specific QoS class;
- UE: User Equipment, any device (e.g., smartphone, laptop, tablet) attached
  to an LTE network.

{::boilerplate bcp14}

# DiffServ Code

Given the semantic equivalence of a Loss-Latency trade-off with the Non Queue
Building (NQB) behaviour, this document reuses the NQB DSCP as-is.

# Marking

Endpoints SHOULD mark packets that are latency sensitive using the NQB DSCP.

Note that the marking could also be added by the path before the packet enters
the LTE core, for example by a classifier in the SGi-LAN.  The disadvantage of
this approach is that it only works for downlink flows, but it might be the
only way to benefit flows that are not marked by their sending endpoint.

# Mobile Network Behaviour

The Mobile network is configured to give UEs a dedicated low-latency EPS
bearer with QCI 7 in addition to the default EPS bearer.

A packet carrying the NQB DSCP SHOULD be routed through the dedicated
low-latency EPS bearer.  A packet that has no associated NQB marking SHOULD be
routed through the default EPS bearer.

# On Remarking and Bleaching

The aggressive remarking / bleaching behaviour described in {{Custura}} is not
a concern for downlink traffic that is by definition completely handled within
the mobile network domain.  On the other hand, NQB marking associated with
uplink traffic generated by the UE MUST be preserved when forwarding via an
interconnect.  In fact, NQB has end-to-end semantics and could be used by
other path elements if the bottleneck link happens to be located somewhere
else than the mobile access.

# IANA Considerations

This document makes no request to IANA.

# Security Considerations

Internet applications cannot benefit from wrongly indicating low-latency as
they have to pay the expense of high loss as a trade-off.  Hence there is no
incentive for Internet applications to set the wrong code-point.

The signal is not integrity protected and could be flipped by an on-path
attacker.  This might negatively affect the QoS of the tampered flow.

# Privacy Considerations

As described in {{Shbair}} state of art encrypted traffic analysis based
machine learning can successfully identify the type of transported application
(e.g., HTTPS, SMTP, P2P, VoIP, SSH, Skype) with good accuracy and without any
need to access the clear-text.  In this context, and despite its limitations
(i.e., fuzzy, coarse grained), a 1-bit signal such as the one described in
this document might be used to improve the precision of the classifier.

# Acknowledgments

We would like to thank the authors of the "Latency Loss Tradeoff PHB Group"
{{I-D.you-tsvwg-latency-loss-tradeoff}} draft: Jianjie You, Michael Welzl,
Brian Trammell and Kevin Smith.  Big thanks to Chris Seal, Dan Druta, Diego
Lopez, Shamit Bhat, Georg Mayer, Florin Baboescu, James Gruessing for the
help.

This work is partially supported by the European Commission under Horizon 2020
grant agreement no. 688421 Measurement and Architecture for a Middleboxed
Internet (MAMI), and by the Swiss State Secretariat for Education, Research,
and Innovation under contract no. 15.0268.  This support does not imply
endorsement.
