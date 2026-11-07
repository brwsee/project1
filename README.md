# 🎙️ المكتبة المعرفية الذكية (Smart Knowledge Library)

تطبيق متكامل **يعمل بدون إنترنت أولاً (Offline-First)** لتسجيل الملاحظات الصوتية القصيرة (حتى **دقيقتين / 120 ثانية** كحد أقصى صارم)، حفظها محلياً مع التاريخ والوقت، وتفريغها صوتياً إلى نص عربي مكتوب تلقائياً فور الاتصال بالإنترنت باستخدام **Gemini API**، مع محرك بحث عربي ذكي وفوري.

---

## ✨ المميزات الأساسية المنفذة بالكامل (v6 النهائية)

1. **تسجيل صوتي حقيقي بحد أقصى دقيقتين (`120 ثانية`)**:
   - تنبيه بصري عند الثانية `105` وإيقاف وحفظ تلقائي عند الثانية `120`.
   - ضغط صوتي فائق الكفاءة (`Opus 32kbps @ 16kHz Mono` أو `WebM/MP4`).
2. **العمل الكامل بدون إنترنت (Offline-First PWA & Mobile)**:
   - إمكانية منح صلاحية الميكروفون مرة واحدة وتثبيت التطبيق على شاشة الهاتف (`manifest.json` + `sw.js`).
   - التسجيل في الأماكن البعيدة عن الإنترنت وحفظ الملف الصوتي (`Audio Blob` / `AES-256-GCM`) داخل ذاكرة الجهاز.
3. **التفريغ التلقائي فور عودة الإنترنت (Auto-Sync & Auto-Transcription)**:
   - مراقب ذكي للشبكة (`online` event + فحص دوري كل 6 ثوانٍ + `visibilitychange`) يكتشف أي مقطع مسجل بدون إنترنت ويقوم بتحويله تلقائياً إلى نص عربي مكتوب عبر مفتاح **Gemini API** المجاني (`gemini-3.8-flash` / `gemini-2.5-flash` / `gemini-2.0-flash`).
4. **عرض موحد ونظيف في بطاقة التسجيل**:
   - تسجيل وعرض **📅 التاريخ** و **⏰ الوقت** تلقائياً لكل مقطع.
   - عرض النص المفرغ في **خانة واحدة موحدة** تحت عنوان **`📝 التفريغ الصوتي`** بدون طوابع زمنية مجزأة (`[00:04]`) أو عناوين مكررة.
   - إمكانية تعديل النص يدوياً، أو الضغط على زر **«🔄 تفريغ النص الآن»**، أو حذف التسجيل، أو الاستماع للمقطع الصوتي الأصلي.
5. **أمان، بحث فوري، ومزامنة متقدمة**:
   - بحث عربي فوري يتجاهل التشكيل والهمزات (`أ، إ، آ -> ا`).
   - تشفير محلي `AES-256-GCM` وقاعدة بيانات `SQLCipher` + `FTS5` في نسخة Flutter.
   - مزامنة خالية من التعارض عبر ساعة منطقية هجينة (`HLC - Hybrid Logical Clock`) ورفع متقطع (`TUS v1.0.0`).

---

## 📂 هيكلية ملفات المشروع الكاملة

```text
smart_knowledge_library/
├── index.html                        # تطبيق الويب التفاعلي الكامل (PWA) - جاهز للتشغيل المباشر أو الرفع على Vercel
├── manifest.json                     # ملف تعريف تطبيق الويب التقدمي (PWA) للتثبيت على الهاتف والعمل بدون إنترنت
├── sw.js                             # Service Worker لتخزين التطبيق وتشغيله بدون إنترنت (Offline)
├── vercel.json                       # إعدادات النشر المباشر على منصة Vercel
├── web_preview/
│   └── index.html                    # نسخة معاينة مطابقة لـ index.html
│
├── database/
│   └── schema.sql                    # مخطط قاعدة البيانات الكامل (SQLite + FTS5 + Upload Queue + Custom Vocab)
│
├── lib/                              # الكود المصدري الكامل لتطبيق الموبايل (Flutter - Android & iOS)
│   ├── main.dart                     # نقطة الدخول وواجهة المستخدم العربية (RTL)
│   ├── core/
│   │   ├── constants/app_constants.dart          # الثوابت الهندسية (حد الـ 120 ثانية، إعدادات Opus، TUS)
│   │   ├── security/encryption_service.dart      # تشفير الصوت AES-256-GCM ومفاتيح SQLCipher
│   │   ├── sync/hlc_clock.dart                   # الساعة المنطقية الهجينة (HLC) لمنع تعارض المزامنة
│   │   ├── sync/conflict_resolver.dart           # خوارزمية دمج التعديلات بين الأجهزة
│   │   ├── utils/arabic_text_evaluator.dart      # محرك تطبيع النص العربي وحساب دقة التفريغ (Normalized WER)
│   │   ├── benchmark/golden_arabic_dataset.dart  # قاعدة بيانات الاختبار القياسية للهجات العربية الـ 5
│   │   └── logging/app_logger_service.dart       # نظام السجلات الدائري (Rotating JSONL Logger)
│   ├── domain/
│   │   └── models/knowledge_entry.dart           # نماذج البيانات (KnowledgeEntry, TranscriptSegment, SyncStatus)
│   ├── data/
│   │   └── local/local_database.dart             # قاعدة البيانات المشفرة SQLCipher + محرك البحث FTS5
│   ├── services/
│   │   ├── audio/audio_recorder_service.dart          # مسجل الصوت مع الإيقاف التلقائي عند 120 ثانية
│   │   ├── audio/encrypted_audio_player_service.dart  # مشغل الصوت المشفر في الذاكرة
│   │   ├── stt/on_device_draft_stt_service.dart       # التفريغ المبدئي على الجهاز والقاموس الشخصي
│   │   ├── sync/sync_upload_service.dart              # رفع المقاطع بنظام الأجزاء (TUS 256KB Chunks)
│   │   ├── sync/background_sync_worker.dart           # عامل المزامنة الخلفي (WorkManager)
│   │   ├── storage/storage_quota_service.dart         # الإدارة الذكية لمساحة التخزين
│   │   └── export/transcript_export_service.dart      # تصدير النصوص بصيغ Markdown و SRT و TXT
│   └── presentation/
│       └── screens/settings_and_diagnostics_screen.dart # شاشة الإعدادات والقاموس وتشخيص الأخطاء
│
├── test/
│   └── arabic_evaluator_and_limits_test.dart     # اختبارات الوحدة الشاملة (حد الـ 120 ثانية، WER، HLC، التصدير)
│
├── backend/                          # الخادم السحابي الخلفي (FastAPI + Gemini API + TUS Server)
│   ├── main.py                       # خادم FastAPI الرئيسي للتفريغ الصوتي والمزامنة
│   ├── requirements.txt              # الاعتماديات والمكتبات المطلوبة لبايثون
│   ├── Dockerfile                    # ملف بناء الحاوية السحابية
│   ├── app/                          # وحدات إضافية للخادم (تقييم، مصادقة، نماذج)
│   ├── quality/                      # أدوات قياس جودة التفريغ العربي (WER)
│   └── tests/                        # اختبارات الخادم السحابي (Pytest)
│
├── mobile/                           # نسخة إضافية منظمة لوحدات الموبايل (Clean Architecture)
├── .vscode/                          # إعدادات التشغيل والتصحيح الجاهزة لبرنامج VS Code
│   ├── launch.json
│   └── extensions.json
├── .github/workflows/                # بناء أوتوماتيكي لملف الـ APK عبر GitHub Actions
│   └── build_and_release.yml
├── .env.example                      # نموذج متغيرات البيئة (GEMINI_API_KEY)
├── pubspec.yaml                      # اعتماديات وحزم مشروع Flutter
├── analysis_options.yaml             # قواعد فحص الكود لـ Dart/Flutter
└── railway.json                      # إعدادات نشر الخادم على منصة Railway
```

---

## 🚀 طريقة التشغيل والتطوير (في VS Code أو أي جهاز آخر)

### أولاً: تشغيل تطبيق الويب التفاعلي (PWA) مباشرة (بدون تثبيت أي برامج)
1. افتح مجلد المشروع في **VS Code** (أو افتح المجلد مباشرة من جهازك).
2. اضغط مرتين على الملف [`index.html`](index.html) ليفتح في متصفحك (Chrome أو Edge أو Firefox)، أو استخدم إضافة **Live Server** في VS Code.
3. لتفريغ المقاطع المسجلة بدون إنترنت تلقائياً، ضع مفتاح **Gemini API** المجاني (من [Google AI Studio](https://aistudio.google.com/apikey)) في الشريط العلوي واضغط **حفظ المفتاح**.
4. **لنشر التحديثات على Vercel**:
   - ارفع مجلد المشروع على [vercel.com/new](https://vercel.com/new) أو عبر الأمر:
     ```bash
     npx vercel --prod
     ```

---

### ثانياً: تشغيل وتطوير تطبيق الموبايل (Flutter - Android / iOS)
1. تأكد من تثبيت [Flutter SDK](https://docs.flutter.dev/get-started/install).
2. افتح مجلد المشروع في **VS Code** وشغّل الأوامر التالية في الطرفية (Terminal):
   ```bash
   flutter pub get
   flutter test
   flutter run
   ```
3. لبناء ملف تثبيت أندرويد (`APK`) نهائي:
   ```bash
   flutter build apk --release
   ```

---

### ثالثاً: تشغيل الخادم السحابي (FastAPI Backend)
1. تأكد من تثبيت Python 3.11+.
2. انسخ ملف `.env.example` إلى `.env` وأضف مفتاح `GEMINI_API_KEY`.
3. شغّل الأوامر التالية:
   ```bash
   pip install -r backend/requirements.txt
   uvicorn backend.main:app --reload --port 8000
   ```

---

## 🤖 كيف تطور هذا المشروع مستقبلاً باستخدام ChatGPT أو Gemini؟

المشروع مُصمم ليكون **مستقلاً تماماً (100% Standalone)** ولا يعتمد على Antigravity في أي جزء منه.
عندما تريد إضافة ميزة جديدة عبر **ChatGPT** أو **Gemini**:

1. **إذا كنت تريد تعديل تطبيق الويب/الموبايل التفاعلي (الذي رفعته على Vercel)**:
   - ارفع ملف [`index.html`](index.html) فقط إلى ChatGPT أو Gemini، واكتب له التعديل المطلوب (لأن `index.html` يحتوي على الواجهة + التسجيل + الحفظ المحلي IndexedDB + الربط مع Gemini API في ملف واحد متكامل).
2. **إذا كنت تريد تعديل تطبيق Flutter أو الخادم السحابي**:
   - ارفع ملف `README.md` مع الملف المراد تعديله من مجلد `lib/` أو `backend/main.py` أو `database/schema.sql`.
   - يمكنك نسخ الرسالة التالية وإرسالها لـ ChatGPT أو Gemini في بداية المحادثة:
     > *"هذا مشروع المكتبة المعرفية الذكية (Offline-First Arabic Voice Note & Gemini Transcription App). المرفق ملف README.md وملف الكود المصدري. أريد الحفاظ على قيد الـ 120 ثانية للتسجيل، التفريغ التلقائي عند عودة الإنترنت في خانة واحدة تحت عنوان (التفريغ الصوتي)، وحفظ التاريخ والوقت. المطلوب تعديله هو: [...]"*
