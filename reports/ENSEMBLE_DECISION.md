# قرار التجميع

KEEP SINGLE — Logistic

The nested forward OOF comparison used 2,155 requests across three forward periods. Logistic Regression was retained as the single model. Its mean Average Precision was approximately 0.392 with fold SD approximately 0.033. The ensemble alternatives did not pass the worth-it gate: Equal AP was about 0.377, Weighted about 0.389, and Stack about 0.383, with passes_gate=False. Therefore the additional ensemble complexity was not justified by a sufficient OOF AP improvement over the best single model. The final choice is Logistic Regression rather than forcing an ensemble to win.

Model selection used nested forward out-of-fold validation rather than random splitting. There were 2,155 OOF requests across three forward periods, while warm-up observations had no outer OOF prediction. This design preserves temporal ordering and reduces leakage between training and later validation periods. However, the three forward folds are not independent confidence intervals and they do not guarantee future performance. The results describe performance on the observed forward periods and can still be affected by future temporal or population drift.

الدليل: artifacts/ensemble_comparison.csv وday5_ensemble_gate.json. SD وصفي، وليس اختبار دلالة.
