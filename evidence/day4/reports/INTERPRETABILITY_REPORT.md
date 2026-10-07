# تقريرك: التفسير والمعايرة — Tamweel Lite

**الحالة:** جاهز للمراجعة؛ لا يعني اعتمادًا أو درجة

**مصدر التفسير:** LIVE. **النموذج والمعايرة:** LIVE. **السعة:** CAPACITY_REVIEW_REQUIRED.

## النموذج والأدوار
LightGBM موزون، 80 شجرة. الهدف حدث تعثر اصطناعي خلال90 يومًا بعد الطلب. الأدوار منفصلة زمنيًا وبالعملاء: تدريب 2516، معايرة 584 (40 موجب)، سياسة 589، تقييم 1733. الفجوات والتداخلات مستبعدة. سبق استخدام بيانات التقييم في الدورة، فهي ليست اختبارًا نهائيًا لم يمسّ.

## التفسير العام والمحلي
Permutation يقيس انخفاضAP على التقييم؛ إشارات المنطقة تُبدّل معًا. SHAP يفسر النموذج الخام بوحدةlog-odds وخلفية مسارات أشجار التدريب. base+sum(SHAP)=raw margin، ثمsigmoid للمجموع فقط. القيم ليست نقاط احتمال ولا تفسيرًا مباشرًا للنموذج المعاير.

The global SHAP explanation ranks bureau_score as the strongest feature (mean absolute SHAP = 0.9042 log-odds), followed by dti (0.5437) and loan_amount_sar (0.3490). Global importance describes average model behaviour across the sampled evaluation requests. In contrast, the local explanation for request TR-009585 explains one individual prediction: its raw score was 0.5931, and bureau_score = 497 contributed approximately +2.27 log-odds, while dti = 1.2081 contributed approximately +1.20 log-odds. Therefore, global importance should not be interpreted as the explanation for every individual request.

The SHAP values are contributions to the model's raw log-odds, not changes in predicted probability. For example, bureau_score has global mean absolute SHAP of 0.9042 log-odds, and for request TR-009585 bureau_score contributed about +2.27 log-odds. These values therefore describe movement in the model's raw decision function and should not be interpreted as probability-point changes.

الطلب الاصطناعي TR-009585: الدرجة الخام 0.90308 والاحتمال المعاير 0.47952. اختير أعلى درجة داخل عينةSHAP دون استخدام النتيجة الفعلية.
- استخدم النموذج درجة ائتمانية اصطناعية عند الطلب بالقيمة 497 لرفع درجته الخام؛ لا يثبت ذلك السببية. (SHAP=+2.2693 log-odds؛ قيمة معوضة: False)
- استخدم النموذج نسبة الالتزام مع القسط المقترح إلى الدخل بالقيمة 1.2806 لرفع درجته الخام؛ لا يثبت ذلك السببية. (SHAP=+1.1981 log-odds؛ قيمة معوضة: False)
- استخدم النموذج مبلغ التمويل المطلوب بالقيمة 93437.3 لرفع درجته الخام؛ لا يثبت ذلك السببية. (SHAP=+0.2007 log-odds؛ قيمة معوضة: False)

The generated reason statements identify influential features for a specific model prediction, but they do not establish causality or prove why an applicant will default. Correlated features may share or redistribute model signal. The permutation results explicitly show this issue: bureau_score and dti have the largest held-out AP drops, while correlated features can share signal and make individual importance unstable. The reason statements should therefore be interpreted as model explanations rather than causal conclusions.

## دليل المعايرة
على 1733 صفًا و139 موجب: Brier 0.113027 → 0.067112؛ ECE 0.146871 → 0.022486. عشر حاويات متساوية العرض مع أعدادها فيday4_reliability_bins.csv. AP 0.258677 → 0.258677؛ ROC-AUC 0.770804 → 0.770804. هذه نتائج هذه العينة وليست ضمانًا لتحسن مستقبلي.

Calibration was evaluated on a separate 1,733-request evaluation period with 139 positive outcomes. Sigmoid calibration left discrimination unchanged (ROC-AUC = 0.770804 and average precision = 0.258677) while improving probability calibration: Brier score decreased from 0.113027 to 0.067112 and ECE decreased from 0.146871 to 0.022486. Thus calibration improved agreement between predicted probabilities and observed event rates without changing the model's ranking performance.

## الاستقرار
200 تكرارbootstrap صالح بسحب العملاء؛ فترات مئينية95% مع تثبيت النموذج والمعاير. لا تشمل تعلم النموذج أو المعايرة أو الانجراف المستقبلي، ولا تصف احتمال فرد. انحرافAP بين ربعي التقييم وصفي فقط. اختبارbureau_score±1 نُفذ؛ راجع day4_local_stability.csv.

The paired customer-cluster bootstrap used 200 replicates while keeping each customer's requests together. The measured Brier improvement from calibration was about -0.04592, with a 95% percentile interval approximately [-0.05418, -0.03741]. Because the interval remains below zero, the measured Brier improvement is stable under these evaluation-data resamples. However, this interval conditions on the fitted model and calibrator and does not include uncertainty from model training, calibration fitting, or future temporal drift; it is not a confidence interval for future production performance.

## العتبة ومنطقة المراجعة
العتبة الخام 0.5881953696965011 اختيرت علىpolicy بخسارة10×FN+FP وسقف12% ثم نُقلت إلى 0.17331013263107387. لم تعدل باستخدام التقييم. المنطقة[0.15331, 0.19331] تشخيصية بعرض±0.02 وليست فترة ثقة. الاتحاد يحسب الطلب مرة واحدة.
- 2024Q3: السقف 100، الإشارات 97، اتحاد المراجعة 109.
- 2024Q4: السقف 107، الإشارات 109، اتحاد المراجعة 122.

The transported calibrated threshold was 0.1733. In period 2024Q3, capacity was 100 requests, while 109 requests were near-threshold review candidates, so the period was not within capacity. In 2024Q4, capacity was 107 requests and 122 requests were review candidates, so it was also not within capacity. Therefore, the review zone exceeds available capacity in both reported periods and would require an operational capacity decision rather than simply reviewing every candidate.

عند تجاوز السعة، وثّق الحاجة إلى تصميم سياسة جديدة على بيانات تطوير وتقييمها بدليل جديد. لا ترفع السقف ولا تقص الحالات بعد رؤية النتيجة. التفسير ليس سببية أو شهادة عدالة، والخسارة وحدات تعليمية لا رسوم أو خصم درجات. لا يستخدم هذا التمرين لتمويل حقيقي.
