# SHARP-Distill

### Official Review of Submission8126 by Reviewer scTP:

**4. Lacks formal proofs or detailed justification**

While SHARP-Distill does not introduce new theoretical proofs, its design is grounded in established theoretical frameworks:

- **Structure-preserving distillation**: Transfers teacher knowledge via probability distributions, building on the theoretical foundations established by [1].
- **InfoNCE-based contrastive learning**: Aligns teacher-student representations using principles from mutual information maximization [2].
- **Hypergraph spectral filtering**: Efficiently captures high-order relations through proven spectral convolution operations [3].

We validate these theoretical foundations empirically using Centered Kernel Alignment (CKA) analysis, which quantitatively measures representation similarity between neural networks. Higher CKA scores indicate better alignment between the representation spaces:

**Table 2: CKA Between Teacher and Student**
| Dataset       | HGNN Embedding | DeBERTa Embedding | SHARP-Distill (Student) |
|---------------|----------------|-------------------|--------------------------|
| Amazon-CDs    | 0.72           | 0.78              | **0.86**                 |
| Yelp          | 0.69           | 0.74              | **0.84**                 |
| Cellphones    | 0.75           | 0.80              | **0.88**                 |
| Beauty        | 0.70           | 0.76              | **0.85**                 |
| Sports        | 0.68           | 0.73              | **0.83**                 |

The consistently high CKA scores (0.83-0.88) demonstrate that our student model effectively captures both structural and semantic knowledge from the teacher components. Notably, the student model achieves higher CKA scores than either individual teacher component, confirming that our distillation process successfully combines both knowledge sources. These empirical results align with theoretical expectations from knowledge distillation literature, where well-designed distillation approaches can create more coherent representations than their teachers through the regularization effect of the distillation process. 
We believe the simplified deployment architecture, minimal inference resource requirements, and strong theoretical grounding supported by CKA analysis directly addresses the implementation and theoretical concerns raised. SHARP-Distill offers not just state-of-the-art performance, but a practical, deployment-ready solution that balances implementation complexity with significant performance gains. We respectfully request that the reviewer reconsider their assessment in light of this additional information.


[1] Hinton G, Vinyals O, Dean J. Distilling the knowledge in a neural network. arXiv preprint arXiv:1503.02531. 2015 Mar 9.
[2] Oord AV, Li Y, Vinyals O. Representation learning with contrastive predictive coding. arXiv preprint arXiv:1807.03748. 2018 Jul 10.
[3] Feng Y, You H, Zhang Z, Ji R, Gao Y. Hypergraph neural networks. InProceedings of the AAAI conference on artificial intelligence 2019 Jul 17 (Vol. 33, No. 01, pp. 3558-3565).
----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------


### Official Review of Submission8126 by Reviewer zAKq:

**Other Strengths and Weaknesses**
Weaknesses:

**1)**

We thank the reviewer for this detailed observation and the pointer to related contrastive distillation works. While SHARP-Distill shares broad goals with [4–6], we respectfully clarify key distinctions in motivation, architecture, and technical contributions:

A. **Cross-modal distillation from a multimodal teacher:**  
   Unlike [4] and [5], which focus on distillation within homogeneous language models (e.g., BERT → RoBERTa), and [6], which uses standard GNNs, SHARP-Distill transfers knowledge from a multimodal teacher (HGNN + DeBERTa) to a compact student (single-layer GCN). This combination of **hypergraph reasoning and textual semantics** for distillation has not been previously studied to our knowledge.

B. **Hypergraph-aware contrastive distillation:**  
   Prior works rely on conventional graph structures. In contrast, we leverage HGNNs to encode high-order group-level user-item relations, which are particularly valuable in sparse recommender data. This enables **structure-level contrastive transfer** beyond pairwise GNN edges.

C. **Novel positional similarity objective:**  
   We introduce a **contrastive alignment module** that matches user/item positions across the hypergraph using incidence matrix powers (Equation 16). This positional view ensures that users with similar neighborhood structure are embedded closely in the student model—extending contrastive distillation into **relational topology space**, which is not explored in [4–6].

D. **Joint fusion beyond simple concatenation:**  
   Our framework includes separate semantic and structural encoders, contrastive objectives for both modalities, and a latent-level fusion mechanism—not raw concatenation. This is detailed in Section 4 and supported by ablation studies.

**Summary of Novel Contributions:**

- Heterogeneous distillation from a hypergraph-textual teacher to a compact student  
- High-order structural signal transfer using HGNN  
- A novel positional alignment loss for structure-aware contrastive distillation  
- Real-time deployable architecture with strong empirical performance  

We hope these clarifications help better position SHARP-Distill’s contributions relative to prior work, and we welcome suggestions to make these distinctions clearer in the final version.

**2)**

We agree and will move the DeBERTa details from the Preliminaries to the appendix to improve focus and clarity in the main text.

**3) On Target Task and Equation (8) – Implicit vs. Rating Prediction**

We thank the reviewer for pointing this out. The target task is **implicit feedback recommendation**, where user–item interactions are binary (e.g., click/no-click, purchase/no-purchase). Equation (8) applies a **pointwise MSE loss** over these binary labels to model interaction likelihood—this is a common approach in top-K recommendation models such as NeuMF [He et al., 2017] and LightGCN [He et al., 2020].

We acknowledge that Eq. (8) was mistakenly labeled as BPR in an earlier draft; this has been corrected. All evaluation metrics (P@10, R@10, NDCG@10, HR@10) are consistent with implicit feedback settings and focus on top-K ranking. We will ensure that the **notation, task framing, and loss function descriptions** are aligned and clearly presented in the final version. We appreciate the reviewer’s attention to this point.

**4) On Equation (8) and Loss Function Clarification**

We thank the reviewer for pointing this out. We confirm that Equation (8) implements a **pointwise MSE loss** over binary labels (0/1), which aligns with our target task of **implicit feedback recommendation**. This design follows prior work such as NeuMF [He et al., 2017] and DeepCF [Deng et al., 2019], where MSE is commonly used for modeling interaction likelihood in implicit settings.

The earlier reference to BPR was a **notation error**, not a conceptual one. We fully acknowledge that BPR is a pairwise ranking loss, while MSE is a pointwise loss. In our case, MSE was chosen intentionally for its stability on binary feedback and its compatibility with downstream ranking metrics (P@10, NDCG@10, HR@10). We have corrected the label and revised the description to ensure consistency between the loss function and task formulation. We appreciate the reviewer’s close reading and attention to this detail.


**5) On Formula and Notation Inconsistencies**

We thank the reviewer for carefully identifying these issues. We address each point below and will correct all inconsistencies in the revised version:

- **Eq. (9) vs. Figure 1:**  
  The formula incorrectly used the student-side loss term `$L_{con}^{XS}$` in the teacher loss. As shown in Figure 1, the correct teacher losses are based on review and interaction views. We will revise Eq. (9) to:  
  `L_teacher = L_bpr + λ (L_con^R + L_con^I)`,  
  ensuring a clear distinction between teacher and student objectives.

- **Undefined `j` in Eq. (7):**  
  We will define `j` as the index of the positive sample in the contrastive pair for the student loss.

- **Undefined `N_u` in Eq. (12):**  
  We clarify that `N_u` refers to the number of users in the sampled mini-batch during contrastive training.

- **Ambiguous degree matrix `D`:**  
  We agree this should be defined as the degree matrix of `A^s + I` (adjacency with self-loops) to ensure correct normalization. This will be clarified in the updated text.

We have reviewed all formulas and figures to ensure alignment and will provide a fully consistent and annotated version in the final paper. We appreciate the reviewer’s attention to detail.


**Other Comments or Suggestions:**

**1 - On Equation (16) and Positional Knowledge Transfer**

We thank the reviewer for raising this important point. We are happy to elaborate on the design and motivation behind Equation (16), which is central to transferring positional knowledge from the hypergraph-based teacher to the lightweight student model.

**Background and Motivation:**  
Standard HGNNs propagate messages across hyperedges and capture high-order structure implicitly, but they do not yield explicit positional encodings of nodes. SHARP-Distill addresses this gap by extracting structural roles from the hypergraph and using them as alignment signals during distillation.

**Mechanism:**  
The positional encodings `P_u` and `P_v` in Eq. (16) are derived from hypergraph incidence patterns. These capture:

- The diversity and frequency of a node’s hyperedge participation  
- High-order proximity and centrality in group interactions  
- The overall structural role of each node in the hypergraph topology  

Eq. (16) defines a similarity function that fuses semantic alignment (`Z_t`, `Z_s`) with positional similarity (`P_u`, `P_v`). This hybrid measure forms the contrastive supervision signal, encouraging the student to capture both content and structural context from the teacher.

**Benefits:**  
(a) Enables hypergraph-aware distillation even though the student does not access hyperedges at inference time  
(b) Provides topology-informed contrastive guidance, improving embedding quality in compact models like a 1-layer GCN or MLP  

To our knowledge, this is the first contrastive distillation framework that incorporates hypergraph-derived positional encoding. We will revise the paper to clarify and highlight the novelty and effectiveness of this approach.

Figure 1 presents an ablation study comparing SHARP-Distill with and without positional encoding (PE) in the student model on the Yelp and Amazon CDs datasets. Incorporating PE significantly improves both Precision@10 and Recall@10 by enabling the student to capture high-order structural roles learned by the teacher, despite relying on a shallow CompactGCN. These results highlight the effectiveness of position-aware contrastive alignment in enhancing representation quality under constrained model capacity.

![CL_Pos](https://github.com/user-attachments/assets/2dd11ce8-8f8b-473d-9d8f-b28ddefb85fb)


**2 - Figure 7)**

We agree that presenting them as subfigures would improve clarity and visual organization, and we will revise the figure layout accordingly in the final version.
