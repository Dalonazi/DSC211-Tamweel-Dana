# بطاقة قرارك — Tamweel Lite

**الحالة:** جاهزة للمراجعة؛ لا تعني اعتمادًا أو درجة
**مصدر الأرقام:** LIVE · **الاستراتيجية:** weighted · **الصفوف:** 5,039 OOF

**المهمة:** الفئة الموجبة `default_within_90d=1` تعني حدث تعثر اصطناعي خلال90 يومًا بعد الطلب. كل طية تحقق طلبات لاحقة، وتستبعد عملاءها من التدريب وتشترط نضج نتيجة التدريب قبل بدايتها. المعرّفات والتاريخ خارج المدخلات.

| الدليل | القيمة |
|---|---:|
| العتبة المقيدة، بالقيمة الكاملة | 0.6583471436014694 |
| عتبة أقل خسارة دون قيد | 0.44863935722081005 |
| Recall | 40.89% |
| Precision | 29.85% |
| AP مجمع منOOF | 0.3100 |
| الإشارات | 526 من 5,039 |
| FN / FP | 227 / 369 |
| الخسارة التعليمية | 2639 وحدة |
| خسارة0.5 | 2403 وحدة؛ ضمن السعة: False |
| التغير عن0.5 | +236 وحدة؛ الموجب زيادة |
| خسارة لكل10,000 طلب، تطبيع حسابي | 5237.15 وحدة |
| فجوة معدل الإنذار الخاطئ بين المناطق | 0.648 نقطة مئوية |

**القاعدة:** درجة ≥ 0.6583471436014694 تعني إشارة مراجعة داخل التمرين؛ غير ذلك بلا إشارة. لا تتخذ موافقة أو رفض تمويل حقيقي. احفظ الدقة الكاملة؛ تقريب العتبة قد يغيّر حجم الطابور.

**السياسة:** FN=10 وFP=1 وحدات تعليمية، وسعة 12% لكل فترة بعد التقريب لأسفل. ليست ريالات فعلية أو رسوم أدوات أو خصمًا من الدرجة.

**دليل السعة:** الفترة 1: 137/195, الفترة 2: 183/200, الفترة 3: 206/207.

## لماذا اخترت هذه العتبة؟
The selected threshold is 0.6583 because it is the minimum-loss decision rule that remains feasible under the 12% operational capacity

## الخسارة والسعة
Threshold selection balances the cost of missed defaults against the operational capacity available to review flagged requests. Lowering the threshold can capture more positives but flags more applications and can exceed the 12% review-capacity limit. Sensitivity analysis also shows that the same threshold remains selected when false-negative cost is varied from 8 to 12, although the resulting loss changes from 2,185 to 3,093 units.

## فرق المناطق وما يحتاج إلى مراجعة
Using the same shared threshold, the observed false-positive rates are 8.09% for central, 8.29% for western, 7.72% for eastern, and 7.64% for other. The maximum observed FPR gap is about 0.648 percentage points. All groups have more than 1,000 negative cases, so none receives the low-support warning. These are descriptive group differences and should not be interpreted as a statistical significance test, fairness certification, or causal effect of region.

## حدود النتيجة
The decision analysis is based on out-of-fold development predictions from 5,039 synthetic requests rather than an independent final test. The scores are not calibrated probabilities, so the theoretical cost-based threshold formula cannot be applied directly.

OOF تغطي 50.39% من التدريب و100% من الصفوف المؤهلة؛ 4,961 صفًا تمهيديًا بلا تنبؤ. اختيار العتبة وتقدير خسارتها هنا يستخدمان أهدافOOF نفسها؛ هذه نتيجة تطوير لا اختبار نهائي. لم نستخدم التحدي. المقارنة الجغرافية وصفية وليست شهادة عدالة، والأوزان لا تضمن معايرة الدرجات.

## سؤالك الأول: لماذا قد تخدعكAccuracy؟
Accuracy is misleading because defaults are relatively rare. A rule that flags nobody achieves 92.38% accuracy on the 5,039 requests while recall is 0, meaning it identifies none of the positive cases. The weighted and oversampled strategies both have much lower accuracy of about 7.62% at the illustrative 0.5 threshold but recall around 0.80. Therefore accuracy alone hides the trade-off between detecting positives and generating false positives in this imbalanced problem.

## سؤالك الثاني: لماذا تختار علىOOF؟
Threshold selection uses out-of-fold predictions so that each request is scored by a model that was not trained on that request. This reduces the optimistic bias that would result from choosing a threshold using training predictions. However, these OOF predictions are still development evidence and are not an independent final test.

أدلتك في `artifacts/threshold_metrics.json` و`day3_period_capacity.csv` و`day3_region_audit.csv` و`day3_cost_sensitivity.csv` و`cost_curve.png`. الحساسية سيناريوهات ±20% لخسارةFN، وليست فترات ثقة. راجع السعة والمعايرة عند تغير البيانات؛ لا تفترض ثباتهما مستقبلًا.
