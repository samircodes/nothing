=IF(R2<=0,
    IF(O2+P2<=0,
        IF(O2<0,
            IF(P2<=0, 0, 0),
            IF(O2=0,
                IF(P2<=0, 0, 0),
                IF(P2<=0, O2+P2, O2)
            )
        ),
        IF(O2<0,
            IF(P2>=0, 0, 0),
            IF(O2=0,
                IF(P2>=0, 0, 0),
                IF(P2<=0, O2+P2, O2)
            )
        )
    ),
    IF(O2+P2<=0,
        IF(O2<0,
            IF(P2<=0, 0, 0),
            IF(O2=0,
                IF(P2<=0, 0, 0)
            )
        ),
        IF(O2<0,
            IF(P2>=0, O2+P2, O2+P2),
            IF(O2=0,
                IF(P2>=0, O2+P2, O2+P2),
                IF(P2<=0, O2+P2, O2+P2)
            )
        )
    )
)



Create Helper Columns for conditions and sub-cases:

Column R: AdjNetRev + TCMAdjPost → Formula: =P2 + O2
Column S: Case 1 (inTotalRPS <= 0) → Formula: =IF(Q2<=0,1,0)
Column T: Sub-case 1.1 (Case1 AND AdjNetRev + TCMAdjPost <= 0) → Formula: =IF(AND(S2=1,R2<=0),1,0)
Column U: Sub-case 1.2 (Case1 AND AdjNetRev + TCMAdjPost > 0) → Formula: =IF(AND(S2=1,R2>0),1,0)
Column V: Case 2 (inTotalRPS > 0) → Formula: =IF(Q2>0,1,0)
Column W: Sub-case 2.1 (Case2 AND AdjNetRev + TCMAdjPost <= 0) → Formula: =IF(AND(V2=1,R2<=0),1,0)
Column X: Sub-case 2.2 (Case2 AND AdjNetRev + TCMAdjPost > 0) → Formula: =IF(AND(V2=1,R2>0),1,0)
LossToRetain Calculation Based on Conditions:

Column Y: Intermediate LossToRetain based on all conditions.
Here are the 14 conditions mapped into formulas for LossToRetain:

Case 1.1 (Sub-case 1.1):
inAdjNetRev < 0 AND inTCMAdjPost <= 0:
Formula: =IF(AND(T2=1,P2<0,O2<=0),0,0)
inAdjNetRev = 0 AND inTCMAdjPost <= 0:
Formula: =IF(AND(T2=1,P2=0,O2<=0),0,0)
inAdjNetRev > 0 AND inTCMAdjPost <= 0:
Formula: =IF(AND(T2=1,P2>0,O2<=0),0,0)
Case 1.2 (Sub-case 1.2):
inAdjNetRev < 0 AND inTCMAdjPost >= 0:
Formula: =IF(AND(U2=1,P2<0,O2>=0),0,0)
inAdjNetRev = 0 AND inTCMAdjPost >= 0:
Formula: =IF(AND(U2=1,P2=0,O2>=0),0,0)
inAdjNetRev > 0 AND inTCMAdjPost <= 0:
Formula: =IF(AND(U2=1,P2>0,O2<=0),P2+O2,0)
inAdjNetRev > 0 AND inTCMAdjPost > 0:
Formula: =IF(AND(U2=1,P2>0,O2>0),P2,0)
Case 2.1 (Sub-case 2.1):
inAdjNetRev < 0 AND inTCMAdjPost <= 0:
Formula: =IF(AND(W2=1,P2<0,O2<=0),0,0)
inAdjNetRev = 0 AND inTCMAdjPost <= 0:
Formula: =IF(AND(W2=1,P2=0,O2<=0),0,0)
inAdjNetRev > 0 AND inTCMAdjPost <= 0:
Formula: =IF(AND(W2=1,P2>0,O2<=0),0,0)
Case 2.2 (Sub-case 2.2):
inAdjNetRev < 0 AND inTCMAdjPost >= 0:
Formula: =IF(AND(X2=1,P2<0,O2>=0),P2+O2,0)

inAdjNetRev = 0 AND inTCMAdjPost >= 0:
Formula: =IF(AND(X2=1,P2=0,O2>=0),P2+O2,0)

inAdjNetRev > 0 AND inTCMAdjPost <= 0:
Formula: =IF(AND(X2=1,P2>0,O2<=0),P2+O2,0)

inAdjNetRev > 0 AND inTCMAdjPost > 0:
Formula: =IF(AND(X2=1,P2>0,O2>0),P2,0)
