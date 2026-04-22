# Example on checking xAODs in python

_This document was originally written by Dr. Shawfeng Dong_

This repo contains a few examples for processing [ATLAS](https://atlas.cern/)
xAOD data.

## What information is in an xAOD file

The
[ATLAS Analysis Release](https://atlas-software.docs.cern.ch/analysis/analysis_tutorial/AnalysisSWTutorial/release_setup/)
provides a handy Python
[checkxAOD.py](https://atlas-software.docs.cern.ch/analysis/analysis_tutorial/AnalysisSWTutorial/obj_xaod_content/)
that we can use to learn the container types and container keys in an xAOD file.

First, set up the environment for ATLAS Analysis

```
export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase
source $ATLAS_LOCAL_ROOT_BASE/user/atlasLocalSetup.sh
asetup AnalysisBase,21.2.111
```

Then run `checkxAOD.py` on a sample xAOD file:

```
[shawfeng@cent7a ATLAS]$ which checkxAOD.py
/cvmfs/atlas.cern.ch/repo/sw/software/21.2/AnalysisBase/21.2.111/InstallArea/x86_64-centos7-gcc8-opt/bin/checkxAOD.py
[shawfeng@cent7a ATLAS]$ checkxAOD.py DAOD_SUSY15.11525262._000003.pool.root.1
========================================================================================================================
 File: DAOD_SUSY15.11525262._000003.pool.root.1
------------------------------------------------------------------------------------------------------------------------
    Memory size        Disk Size       Size/Event  Compression Entries  Name (Type)
------------------------------------------------------------------------------------------------------------------------
        14.21 kB          3.50 kB     0.00 kB/event    4.06       855   TrigConfKeys (xAOD::TrigConfKeys_v1) [Trig]
        17.60 kB          4.03 kB     0.00 kB/event    4.36       855   AntiKt4LCTopoJets (DataVector<xAOD::Jet_v1>) [Jet]
        71.30 kB         11.11 kB     0.01 kB/event    6.42       855   Kt4EMTopoEventShape (xAOD::EventShape_v1) [PFO]
       103.82 kB         17.25 kB     0.02 kB/event    6.02       855   HLT_xAOD__TrackParticleContainer_InDetTrigTrackingxAODCnv_Muon_EFID (DataVector<xAOD::TrackParticle_v1>) [Trig]
       158.71 kB         26.15 kB     0.03 kB/event    6.07       855   LVL1EnergySumRoI (xAOD::EnergySumRoI_v1) [Trig]
       141.89 kB         38.54 kB     0.05 kB/event    3.68       855   LVL1MuonRoIs (DataVector<xAOD::MuonRoI_v1>) [Trig]
       165.77 kB         47.94 kB     0.06 kB/event    3.46       855   MET_LocHadTopo (xAOD::MissingETContainer_v1) [MET]
       151.53 kB         49.34 kB     0.06 kB/event    3.07       855   HLT_xAOD__TrigMissingETContainer_TrigEFMissingET_topocl_PUC (DataVector<xAOD::TrigMissingET_v1>) [Trig]
       150.48 kB         49.71 kB     0.06 kB/event    3.03       855   HLT_xAOD__TrigMissingETContainer_TrigEFMissingET (DataVector<xAOD::TrigMissingET_v1>) [Trig]
       150.86 kB         50.13 kB     0.06 kB/event    3.01       855   HLT_xAOD__TrigMissingETContainer_TrigEFMissingET_mht (DataVector<xAOD::TrigMissingET_v1>) [Trig]
       132.85 kB         51.15 kB     0.06 kB/event    2.60       855   HLT_xAOD__ElectronContainer_egamma_Electrons (DataVector<xAOD::Electron_v1>) [Trig]
       319.77 kB         63.15 kB     0.07 kB/event    5.06       855   EventInfo (xAOD::EventInfo_v1) [EvtId]
       242.30 kB         68.48 kB     0.08 kB/event    3.54       855   HLT_xAOD__MuonContainer_MuonEFInfo_FullScan (DataVector<xAOD::Muon_v1>) [Trig]
       179.61 kB         78.31 kB     0.09 kB/event    2.29       855   HLT_xAOD__PhotonContainer_egamma_Photons (DataVector<xAOD::Photon_v1>) [Trig]
       242.57 kB         82.72 kB     0.10 kB/event    2.93       855   MET_Core_AntiKt4EMTopo (xAOD::MissingETContainer_v1) [MET]
       359.62 kB         85.67 kB     0.10 kB/event    4.20       855   HLT_xAOD__MuonContainer_MuonEFInfo (DataVector<xAOD::Muon_v1>) [Trig]
       670.42 kB        109.75 kB     0.13 kB/event    6.11       855   ByteStreamEventInfo (EventInfo_p4) [EvtId]
       365.98 kB        133.39 kB     0.16 kB/event    2.74       855   GSFConversionVertices (DataVector<xAOD::Vertex_v1>) [egamma]
       279.50 kB        136.51 kB     0.16 kB/event    2.05       855   MuonSpectrometerTrackParticles (DataVector<xAOD::TrackParticle_v1>) [Muon]
       430.51 kB        138.66 kB     0.16 kB/event    3.10       855   MET_Reference_AntiKt4EMTopo (xAOD::MissingETContainer_v1) [MET]
       466.98 kB        142.38 kB     0.17 kB/event    3.28       855   VrtSecInclusive_SecondaryVertices (DataVector<xAOD::Vertex_v1>) [*Unknown*]
      1002.16 kB        202.54 kB     0.24 kB/event    4.95       855   LVL1EmTauRoIs (DataVector<xAOD::EmTauRoI_v2>) [Trig]
       476.07 kB        208.92 kB     0.24 kB/event    2.28       855   ExtrapolatedMuonTrackParticles (DataVector<xAOD::TrackParticle_v1>) [Muon]
      8911.50 kB        236.74 kB     0.28 kB/event   37.64       855   xTrigDecision (xAOD::TrigDecision_v1) [Trig]
       511.68 kB        240.21 kB     0.28 kB/event    2.13       855   CombinedMuonTrackParticles (DataVector<xAOD::TrackParticle_v1>) [Muon]
       638.75 kB        311.45 kB     0.36 kB/event    2.05       855   MET_Track (xAOD::MissingETContainer_v1) [MET]
      5424.75 kB        566.53 kB     0.66 kB/event    9.58       855   TauJets (DataVector<xAOD::TauJet_v2>) [tau]
      1486.02 kB        583.31 kB     0.68 kB/event    2.55       855   Muons (DataVector<xAOD::Muon_v1>) [Muon]
      1001.48 kB        622.72 kB     0.73 kB/event    1.61       855   egammaClusters (DataVector<xAOD::CaloCluster_v1>) [egamma]
      2177.31 kB        851.52 kB     1.00 kB/event    2.56       855   METAssoc_AntiKt4EMTopo (xAOD::MissingETAssociationMap_v1) [MET]
      1240.53 kB        861.32 kB     1.01 kB/event    1.44       855   egammaTopoSeededClusters (DataVector<xAOD::CaloCluster_v1>) [egamma]
      2268.96 kB        919.86 kB     1.08 kB/event    2.47       855   MuonSegments (DataVector<xAOD::MuonSegment_v1>) [Muon]
      1288.68 kB        936.54 kB     1.10 kB/event    1.38       855   InDetForwardTrackParticles (DataVector<xAOD::TrackParticle_v1>) [InDet]
     11424.90 kB       1035.51 kB     1.21 kB/event   11.03       855   BTagging_AntiKt4EMTopo (DataVector<xAOD::BTagging_v1>) [BTag]
      2550.01 kB       1252.15 kB     1.46 kB/event    2.04       855   Electrons (DataVector<xAOD::Electron_v1>) [egamma]
      8677.78 kB       1786.35 kB     2.09 kB/event    4.86       855   PrimaryVertices (DataVector<xAOD::Vertex_v1>) [InDet]
      9377.16 kB       1989.75 kB     2.33 kB/event    4.71       855   AntiKt4EMTopoJets (DataVector<xAOD::Jet_v1>) [Jet]
      4405.27 kB       2562.17 kB     3.00 kB/event    1.72       855   GSFTrackParticles (DataVector<xAOD::TrackParticle_v1>) [egamma]
      4534.35 kB       2806.48 kB     3.28 kB/event    1.62       855   Photons (DataVector<xAOD::Photon_v1>) [egamma]
     12708.16 kB       7513.35 kB     8.79 kB/event    1.69       855   VrtSecInclusive_SelectedTrackParticles (DataVector<xAOD::TrackParticle_v1>) [*Unknown*]
     37973.60 kB       9237.21 kB    10.80 kB/event    4.11       855   VrtSecInclusive_All2TrksVertices (DataVector<xAOD::Vertex_v1>) [*Unknown*]
     40923.05 kB      11891.41 kB    13.91 kB/event    3.44       855   TrigNavigation (xAOD::TrigNavigation_v1) [Trig]
     35664.74 kB      21271.48 kB    24.88 kB/event    1.68       855   InDetTrackParticles (DataVector<xAOD::TrackParticle_v1>) [InDet]
------------------------------------------------------------------------------------------------------------------------
    199483.14 kB      69275.38 kB    81.02 kB/event                     Total
========================================================================================================================

================================================================================
         Categorized data
================================================================================
     Disk Size         Fraction    Category Name
--------------------------------------------------------------------------------
       0.013 kb        0.000       PFO
       0.202 kb        0.002       EvtId
       0.663 kb        0.008       tau
       1.211 kb        0.015       BTag
       1.675 kb        0.021       MET
       2.332 kb        0.029       Jet
       2.443 kb        0.030       Muon
       9.635 kb        0.119       egamma
      15.028 kb        0.185       Trig
      19.758 kb        0.244       *Unknown*
      28.064 kb        0.346       InDet
      81.024 kb        1.000       Total

================================================================================
CSV for categories disk size/evt and fraction:
Total,InDet,*Unknown*,Trig,egamma,Muon,Jet,MET,BTag,tau,EvtId,PFO
81.024,28.064,19.758,15.028,9.635,2.443,2.332,1.675,1.211,0.663,0.202,0.013
1.000,0.346,0.244,0.185,0.119,0.030,0.029,0.021,0.015,0.008,0.002,0.000
================================================================================
```

In this case, for example, `AntiKt4LCTopoJets` is a _key name_ for the container
whose type is `DataVector<xAOD::Jet_v1>`. Once you know the container types, you
can find their APIs at the
[RootCore APIs site](http://hep.uchicago.edu/~kkrizka/rootcoreapis/dd/d44/namespacexAOD.html).

## Using xAOD API in python

In your analysis, you choose the key name for the particular instance of the
container in which you are interested (e.g., `AntiKt4LCTopoJets`). **Note** xAOD
APIs are higher-level APIs, and they hide some behind-the-scenes magic, which is
actually saved in the Auxiliary store of the raw Root file. However, as of this
writing, you can only access the xAOD APIs in either C++ (e.g.,
[Exam_JetsPlot.cxx](../../tutorials/201908/bnl.md)) or pyROOT (e.g.,
[pyROOT_example.ipynb](https://github.com/usatlas/tier3docs/blob/master/jupyter/examples/pyROOT_example.ipynb),
but not in [uproot](https://github.com/scikit-hep/uproot) yet. Until someone
implements xAOD support for uproot, you will have to read the raw data from the
Auxiliary store (for an example, see
[convert_specific_variables.py](convert_specific_variables.py.txt)).
