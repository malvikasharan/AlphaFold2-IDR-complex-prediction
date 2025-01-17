# AlphaFold2 IDR complex prediction

This is a follow-up on our previous observations on how AlphaFold2 excells at recognising disordered protein regions from the sequence. For details on how AlphaFold2 functions as a binary disorder prediction, read our notes here: https://github.com/normandavey/ProcessedAlphafold

Here we assess what information can be derived from AlphaFold2 predictions about how disordered proteins interact. Given the constraints in testing sets, running time and the complexity of disordered protein interactions, we do not show comprehensive analyses. Instead we focus on selected examples that highlight various flavours of disordered protein binding.

## *Overview*

AlphaFold was trained to recognise the structure of proteins from the sequence in their monomeric form. However, quickly after the release of AlphaFold2 it became clear that in several cases AlphaFold can successfully predict the structure of protein complexes even without re-designing or re-training the algorithm. Linking two or more proteins with flexible linkers and using this as an input can simluate the interaction between the proteins by co-folding them. This opens up exciting possiblities to explore protein-protein interactions.

Intrinsically disordered proteins/regions (IDPs/IDRs) very often exert their function through interactions with other protein partners. Short binding IDRs often contain short linear motifs (SLiMs) where the interaction with the partner folded domain is driven by a few residues. The bound structures of SLiMs can be helical, irregular or they can form additional beta strands in an existing beta sheet of the partner domain. Longer IDRs can contain arrays of SLiMs and can form longer binding regions with highly varied secondary structure content in the bound form. IDRs can also form stable structures by interacting with each other forming homo- or heterooligomeric complexes.

Here we selected 14 cases of complexes that involve one or more IDRs. The structure of each of these complexes have been experimentally determined and are in PDB. We go through these complexes one by one, describe their distinguishing features and look at how the experimental complex is predicted by AlphaFold2. Here you can look at the predicted and determined structures side-by-side or overlaid in an interactive structure viewer: http://slim.icr.ac.uk/projects/alphafold?page=alphafold_IDR_interface_prediction

After the examples we summarise the factors that seem to influence the success of AlphaFold in identifying the correct complex structure.

## *Running AlphaFold predictions*

All AlphaFold predictions were run using the sequences defined in the PDB files (not including modified residues and other molecules). Predictions were done using the Google Colab notebooks by Sergey Ovchinnikov (@sokrypton), Milot Mirdita (@milot_mirdita) and Martin Steinegger (@thesteinegger). Credit goes to Minkyung Baek (@minkbaek) and Yoshitaka Moriwaki (@Ag_smith) as well for protein-complex prediction proof-of-concept in AlphaFold2.
* homooligomers were predicted using the notebook for monomers/homooligomers, accessible here: https://colab.research.google.com/github/sokrypton/ColabFold/blob/main/AlphaFold2.ipynb
* heterooligomers were predicted using the dedicated notebook, accessible here: https://colab.research.google.com/github/sokrypton/ColabFold/blob/main/AlphaFold2_complexes.ipynb In the case of dimers, the default settings were used. In case of higher order oligomers, one chain was used on its own (usually the IDR if there is only one), and the rest of the chains were concatenated using a long linker (either several 'U's or several repeats of 'SG's)

## *Examples*

In every example folded protein domains are shown in surface representation and IDRs are shown in ribbon. AlphaFold predictions are shown in tan colour, while the experimentally determined structures are shown in purple (or similar) colours. In all cases below AlphaFold predicts the structure of folded domains near-perfectly (typically within 1-2A RMSD). These predicted structures are not shown separately as their surfaces coincides with the surface generated from the experimental structures.

### *Motifs/IDRs binding to single domains*
**NRBOX motif in NRIP1 bound to ERR3**

*Definition:*
Short motif forming an amphipathic *helix* binding into a well defined, single hydrophobic pocket on the surface of the partner domain.
Composition: 1 ordered protein + 1 IDR
PDB:2gpo

*Results:*
RMSD between predicted and experimental ligand conformations: 0.718A
In the original structure only the core motif is visible, AlphaFold also predicts a helical structure for the flanking region. This is due to the fact that AlphaFold is required to assign coordinates for all input residues; however, is assigns a low pLDDT score to this region, correctly marking that it’s a low confidence structure prediction (and low pLDDT is a good predictor of disorder). On the motif region that is responsible for affinity/specificity, the AF model predicts even side chain conformations correctly.

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2gpo_structure_comparison.png" width="500" height="294">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2gpo_lDDT.png" width="500" height="333">
</p>

**RanBP2 SIM (SUMO interacting motif) bound to SUMO**

*Definition:*
Short motif forming an additional strand in the *beta sheet* of the partner domain (beta augmentation).
Composition: 1 ordered protein + 1 IDR
PDB:2las

*Results:*
RMSD between predicted and experimental ligand conformations: 1.279A
AlphaFold correctly identifies the beta augmentation and the conformation of the side chains in the core motif. It does miss some stabilizing contacts at the edges, most notably it places the C-terminal Phe outside of the native binding pocket (flipping upwards in the top right of the image). AlphaFold correspondingly assigns a lower pLDDT value to the terminal regions of the peptide.

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2las_structure_comparison.png" width="500" height="306">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2las_lDDT.png" width="500" height="333">
</p>

**Histone deacetylase 4 14-3-3 phosphomotif bound to 14-3-3gamma**

*Definition:*
A short IDR binding to a groove inside a 14-3-3 domain, adopting an *irregular conformation*. The interaction requires a *phosphorylated serine*.
Composition: 1 ordered protein + 1 IDR
PDB:3uzd

*Results:*
AlphaFold correctly identifies the binding groove and correctly assignes a coil-like conformation to the peptide, but is not able to fit the peptide into the correct position. One of the reasons could be that AlphaFold cannot model modified residues (the pSer in the original structure and the Ser in the predicted structure are shown in red).

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/3uzd_structure_comparison.png" width="500" height="293">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/3uzd_lDDT.png" width="500" height="333">
</p>
 
Using a phosphomimetic (modeling the interaction after switching the Ser to Glu in the peptide) improves the orientation a little but the pSer and Glu occupy very different positions and the confidence score does not improve, so it is likely due to random chance.

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/3uzd_structure_comparison_mimE.png" width="500" height="293">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/3uzd_lDDT-mimE.png" width="500" height="333">
</p>

**pKID binding to the KIX domain**

*Definition:*
The disordered KID region binds to the KIX domain upon phosphorylation. pKID binds to *two distinct patches* on KIX adopting *helical* bound structures. The interaction is *phospho-dependent* and KIX isn’t fully stable in the unbound form, so the prediction of the domain structure might be more challenging than usual.
Composition: 1 ordered protein + 1 IDR
PDB:1kdx

*Results:*
AlphaFold correctly folds the KIX domain and it also correctly folds KID into a helical structure that is very close to the actual bound structure. However, it places KID in a completely different surface on the KIX domain. In contrast to previous errors, here AlphaFold assigns a high confidence to the prediction.
Interestingly, the region occupied by KID in the AlphaFold model is a true binding site, the C-terminal part of the bound FOXO3 TAD region shows a high similarity to KID in the predicted model (PDB:2lqh) (similarly to the lysine N-methyltransferase 2A peptide (PDB:2agh) and p65 peptide (PDB:5u4k) bound structures).

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/1kdx_structure_comparison.png" width="500" height="306">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/1kdx_lDDT.png" width="500" height="333">
</p>

**TAD of RelA bound to the TAZ domain of CREB-binding protein (CBP)**

*Definition:*
The disordered RelA binding region is significantly longer than previous examples. It wraps around the TAZ domain making contacts at *4 distinct patches*, adopting mostly *helical conformations* at these 4 sites.
Composition: 1 ordered protein + 1 IDR
PDB:2lww

*Results:*
AlphaFold correctly folds the TAZ domain of CBP, even though the native structure contains 3 Zn2+ ions, which AF cannot explicitly model (however, it was trained to correctly predict the structures of ion-containing proteins even without ions present). AlphaFold also correctly identifies the four binding surfaces and also folds RelA into helical conformations at these sites. However, in the predicted structure RelA is in the wrong orientation. In the figure below, the measured RelA (purple) wraps counterclockwise with both termini on the bottom. The predicted RelA wraps in the opposite direction, with both termini on the top. AlphaFold again assigns a high confidence score to the peptide prediction.

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2lww_structure_comparison.png" width="500" height="306">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2lww_lDDT.png" width="500" height="333">
</p>

**Cyclin-A2 bound to Cdc20**

*Definition:*
The disordered tail of cyclin-A2 binds to the beta propeller domain of Cdc20 using three motifs binding to *three separate pockets*. The bound structure of the IDR is *coil-like* without any regular secondary structures and the linkers between the three motifs remain disordered even upon binding.
Composition: 1 ordered protein + 1 IDR
PDB:6q6g

*Results:*
AlphaFold folds the domain near perfectly; however, it cannot fold the IDR on the surface of the domain. It does recognize a hydrophobic patch in the IDR (AALAVL) and it places close to the true binding pocket for this bit, however in the wrong conformation (alpha helix instead of a coil-like conformation). The rest of the IDR has no predicted contacts with the domain.

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/6q6g_structure_comparison.png" width="500" height="297">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/6q6g_lDDT.png" width="500" height="333">
</p>

**Phactr1 bound to PP1**

*Definition:*
A disordered part of Phactr1 wraps around the PP1 domain establishing *contacts at several points*. The N-terminal region adopts a *coil-like* structure with two short bits in *beta-augmentation*. The C-terminal region adopts a largely *helical* conformation.
Composition: 1 ordered protein + 1 IDR
PDB:6zee

*Results:*
AlphaFold correctly folds the domain and it very precisely finds the correct position and orientation for the IDR (RMSD=0.903A). The high precision might have something to do with the IDR being asymmetric (helical on one end, beta on the other), adopting regular secondary structure and being fairly long.

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/6zee_structure_comparison.png" width="500" height="297">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/6zee_lDDT.png" width="500" height="333">
</p>

### *Motifs/IDRs binding to dimeric structured partners*

**p27 bound to the CDK2:cyclinA complex**

*Definition:*
p27 is fully disordered with a *long*, 83 residue stretch binding to the ordered CDK2:cyclinA dimer. Upon binding p27 adopts an *elongated structure* containing several short patches of *regular secondary structure* and a longer helix that forms only very weak contacts and serves as a structural linker between the N- and C-terminal binding regions.
Composition: 2 ordered proteins + 1 IDR
PDB:1jsu

*Results:*
AlphaFold folds the domains correctly and they have the right relative orientation. It also assigns a structure to the disordered p27 that is strikingly close to the actual bound conformation; however, it cannot fold it on the surface of the domain dimer. It does find the correct binding pocket for the C-terminal bit, but otherwise predicts p27 to have no contact with the domains.
AlphaFold confidence is high in the prediction, reflecting the confidence in the local structure of p27 (which is indeed close to the actual bound one) and not its relationship to the folded domains. The PAE graph clearly shows that AlphaFold correctly assesses that the relative orientation of p27 (the end of the graph below) compared to the position of the dimers is very low confidence.
Since the predicted p27 conformation is close to the actual bound one even without predicting contacts, it is likely that AlphaFold learned this structure as it was in its training set.

<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/1jsu_structure_comparison.png" width="500" height="306">
<p float="left">
 <img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/1jsu_lDDT.png" width="500" height="333"><img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/1jsu_PAE.png" width="500" height="333">
 </p>

**RGDLxxL motif in the foot-and-mouth disease virus capsid protein bound to integrin $\alphaV$\beta6**

*Definition:*
The *short IDR* contains an RGD motif that binds to the two ordered subunits of the integrin dimer with Arg contacting the $\alphaV subunit and the Asp contacting the $\beta6 subunit by *coordinating a divalent cation* embedded in the interacting integrin domain. In addition, the C-terminal flank of the RGD motif forms a short helix binding to two small hydrophobic patches on the $\beta6 subunit.
Composition: 2 ordered proteins + 1 IDR
PDB:5nem

*Results:*
AlphaFold folds the two folded subunits of the integrin near perfectly (RMSD=0.552). The peptide is not folded into the binding groove; AlphaFold cannot identify the bound conformation or the binding pockets where the peptide would fit. This might be because the peptide is short and because the interaction with the domain is mediated through the coordination of a divalent cation that AF cannot model. Contrary to the p27 example, this IDR is short enough that AlphaFold couldn’t learn the bound conformation.

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/5nem_structure_comparison.png" width="500" height="297">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/5nem_lDDT.png" width="500" height="333">
</p>

### *Complexes with several IDRs*
**GCN4 prototypic leucine zipper**

*Definition:*
A coiled-coil like dimer of two copies of the same *amphipathic helix*, held together by several hydrophobic interactions mediated largely by Leu residues.
Composition: 0 ordered proteins + 2 IDRs
PDB:1zik

*Results:*
AlphaFold gets the structure near perfect (RMSD=0.311A), including the Leu side chains that hold the complex together. It does assign a high confidence to the residues, however, it does mark the relative orientation of the two helices as very uncertain (see PAE figure on the bottom right). This is also expressed in the predicted structure, as the two helices are oriented a bit further away from each other than in the experimental structure. In fact, in several predicted structures of the same complex the two helices are even further apart with AlphaFold predicting no contacts between them.

<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/1zik_structure_comparison.png" width="500" height="306">
<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/1zik_lDDT.png" width="500" height="333"><img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/1zik_PAE.png" width="500" height="333">
</p>

**p53 tetramerization domain**

*Definition:*
Four copies of the C-terminal tetramerization region of p53 forms a dimer of dimers, adopting an *intertwined structure with high helical content* further stabilized with *short beta sheets*.
Composition: 0 ordered proteins + 4 IDRs
PDB:2j0z

*Results:*
Both the conformations and the relative orientation of the four chains is near perfect (RMSD=0.909A). In contrast to the GCN4 example, here AlphaFold is confident in not only the conformation of the residues but also the relative position of the four chains (see PAE figure on the bottom right). This difference might be due to the p53 tetramer being a more intertwined structure with asymmetry in terms of bound structure along the IDRs.

<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2j0z_structure_comparison.png" width="500" height="306">
<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2j0z_lDDT.png" width="500" height="333"><img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2j0z_PAE.png" width="500" height="333">
</p>

**Autophagic SNARE core complex (Vamp8 / Syntaxin-17 / SNAP29)**

*Definition:*
Classical tetrameric coiled-coil (has 2 regions of a SNAP29 and 1-1 region of the other proteins). Since SNARE complexes assemble and disassemble, the unbound disordered state is biologically relevant. The tetramer assembles as a tightly packed fully parallel coiled-coil of four *long and symmetrical helices*.
Composition: 0 ordered proteins + 4 IDRs
PDB:4wy4

*Results:*
AlphaFold correctly assembles the four chains into an overall coiled-coil-like conformation (see top structural figures) and has high confidence in the conformation of the individual residues (see pLDDT figure on bottom left, the low confidence regions are the linkers, which are removed from the structure). Chains C and D are correctly folded and positioned relative to each other. However, AlphaFold shows that it has low confidence in the relative orientation of chains A and B (see PAE figure on bottom right). In accord, both of these chains are placed in a conformation that makes only little contact with the other chains, and the orientation of chain A is reversed, running antiparallel compared to the other chains. This disrupts the naturally tight packing of the coiled-coil tetramer (see bottom-view structure figures in the middle in surface representation).

<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/SNARE-summary.png" width="1000" height="872">
<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/4wy4_lDDT.png" width="500" height="333"><img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/4wy4_PAE.png" width="500" height="333">
</p>

**Rb:E2F1:DP1 heterotrimer**

*Definition:*
A *highly intertwined* trimeric complex with the three IDRs serving as a single folding unit. The resulting structure is fairly compact, *highly asymmetric* and has a *high regular secondary structure content*.
Composition: 0 ordered proteins + 3 IDRs
PDB:2aze

*Results:*
AlphaFold gives a near-perfect prediction on all three chains (overall RMSD=0.996A), including their conformation and relative orientation. AlphaFold also assigns high confidence to the prediction.

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2aze_structure_comparison.png" width="500" height="306">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/2aze_lDDT.png" width="500" height="333">
</p>

**A collagen triple helix binding the A3 domain from the von Willebrand Factor (vWF)**

*Definition:*
The collagen triple helix is built up from three identical chains with very *high glycine and proline content* (with some prolines being modified to hydroxyproline), with each chain adopting a *PPII-like conformation*. This triple helix needs to fold into the correct conformation to create the binding site for the vWF domain, which can bind by making contacts with two out of the three collagen chains. In reality this happens sequentially, AlphaFold needs to build up the whole complex in a single step.
Composition: 1 ordered protein + 3 IDRs
PDB:4dmu

*Results:*
AlphaFold is able to fold the domain from vWF near perfectly (RMSD=0.592 for the domain), but cannot assemble the collagen triple helix - even though it assigns a high confidence to collagen residues. Looks like the high Pro/Gly content is not compatible with a folded structure for AlphaFold. The lack of a hydrophobic core, regular secondary structures and sequence complexity are likely reasons for the lack of success (note: AlphaFold fails to assemble the collagen triple helix even without the vWF domain).

<p float="left">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/4dmu_structure_comparison.png" width="500" height="306">
<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/4dmu_lDDT.png" width="500" height="333">
</p>


### *Predictions outside of the training set*
**Predicting structures of bound LxxLL motifs**

In all previous examples, the structures were taken from PDB (all were released before 2018) and hence they all were part of the AlphaFold2 training set. To see how good AlphaFold is in the prediction of novel bound structures of motifs, we ran it on 10 instances of the LxxLL motif (listed in [the ELM database](http://elm.eu.org/elms/LIG_NRBOX)) that have no solved structures. All of these instances have been verified to be true positives and since they all contain the same motif, we expect them to all bind to the same hydrophobic groove on the domain surface.
In all 10 tested examples AlphaFold correctly folds the peptides into a helical conformation and places them into the correct binding pocket (novel predictions are in transparent cartoons, the experimentally verified structure of the NRIP1 LxxLL motif (PDB:2gpo) is shown in shown in purple):

<img src="https://github.com/normandavey/AlphaFold2-IDR-complex-prediction/blob/main/LxxLL-genuine_predictions_overlay.png" width="500" height="294">




## *Conclusions*

Based on these examples, there are several properties of the IDRs that seem to enable a better prediction on the complex structures:
* Presence of regular secondary structures in the bound IDP conformation (helical conformation or beta augmentation)
* Well defined, hydrophobic binding groove
* Asymmetric bound IDP structure (in terms of secondary structural elements along the IDR sequence)

Other propoerties that seem to decrease the chance of a successful prediction:
* Short IDRs
* Irregular bound structure
* Phosphorylation-dependent binding
* Presence of ions in the interface
* Highly symmetric bound structures, such as long helices or arrays of short similar structural elements

In general, AlphaFold performs remarkably well in predicting structures where IDRs are part of a multi-IDR single folding unit (such as p53 tetramer or E2F1-DP1-Rb) or where the IDR binds to a folded domain driven by hydrophobicity. In other cases AlphaFold often does not find the correct binding mode but even then it often correctly identifies the bound IDR structures (or at least the presence/absence of secondary structures) and the binding sites on domain surfaces (but not always the correct one - such as in the KID/KIX complex).


