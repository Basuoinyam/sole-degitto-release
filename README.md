# Sole d’Egitto — applicazione web

Applicazione multi-pagina per il sito turistico Sole d’Egitto. Frontend in HTML/CSS/JavaScript, API REST con Express e database SQLite.

## Requisiti
- Node.js 20 o superiore (testato nel progetto precedente con Node 22)
- npm

## Installazione locale (Windows)
1. Estrai la cartella `sole-degitto`.
2. Apri il Prompt dei comandi nella cartella.
3. Esegui `npm install`.
4. Copia `.env.example` in `.env`.
5. Genera la password hash: `node scripts/hash-password.js "InserisciUnaPasswordLunga"` (almeno 12 caratteri).
6. Incolla il risultato in `ADMIN_PASSWORD_HASH`, imposta `ADMIN_USERNAME` e sostituisci `SESSION_SECRET` con una stringa casuale lunga.
7. Avvia con `npm start` e visita `http://localhost:3000`.
8. Pannello amministratore: `http://localhost:3000/admin.html`.

Non usare una password di esempio o il secret predefinito in un ambiente pubblico. Non condividere il file `.env`.

## Pagine
- `/index.html` — Home, proposte, sezione recensioni dinamica.
- `/excursions.html` — catalogo escursioni.
- `/excursion.html?id=ID` — dettagli, gallery, calcolo del prezzo e richiesta WhatsApp.
- `/about.html` — Chi siamo.
- `/payments.html` — informazioni sui pagamenti; gateway non attivo.
- `/admin.html` — login e area amministratore protetta.

## Struttura tecnica
- `server.js`: API, autenticazione, sessioni, database, migrazioni e regole business.
- `public/*.html`: pagine.
- `public/css/main.css`: stile condiviso.
- `public/js/`: JavaScript organizzato per pagina e funzioni condivise.
- `public/uploads/`: immagini caricate dal pannello.
- `data/site.sqlite`: database locale (generato automaticamente).
- `data/sessions.sqlite`: sessioni di accesso.

## API principali
- Pubbliche: `GET /api/settings`, `GET /api/excursions`, `GET /api/excursions/:id`, `GET /api/reviews`, `POST /api/reviews`, `POST /api/bookings`.
- محمية: `POST /api/admin/login`, `GET /api/admin/me`, `POST /api/admin/logout`, `GET /api/admin/dashboard`, إدارة الرحلات والإعدادات والحجوزات والتقييمات، `POST /api/admin/upload`, `GET /api/admin/export`.

## قاعدة البيانات
SQLite تنشأ تلقائيًا وتحتوي على الرحلات والإعدادات والعملاء والحجوزات والتقييمات والمستخدمين الإداريين والرسائل ومشاهدات الرحلات. يتم إجراء migrations إضافية بشكل آمن عند تشغيل النسخ الأقدم.

## الأسعار والخصومات
يُحفظ سعر الفرد كقيمة رقمية مع العملة ونسبة الخصم. إجمالي الحجز = سعر الفرد × عدد الأشخاص × (1 - الخصم / 100). يعرض الموقع الإجمالي التقديري، ويسجل الطلب في قاعدة البيانات، ثم يجهز رسالة WhatsApp بالتفاصيل. لا يتم تحصيل أي مبلغ إلكتروني في هذه النسخة.

## الأمان
- كلمات مرور الأدمن محفوظة كـ bcrypt hash.
- جلسات server-side في SQLite وCookie من نوع HttpOnly وSameSite=Lax.
- تدوير Session ID عند نجاح تسجيل الدخول.
- تحديد محاولات تسجيل الدخول ومعدل طلبات API.
- جميع عمليات إدارة البيانات والرفع والتصدير محمية بجلسة الأدمن.
- في الإنتاج: HTTPS، `NODE_ENV=production`، سر جلسة قوي، نسخ احتياطية دورية، وتخزين آمن للصور.

## حدود النسخة الحالية
هذه نسخة تأسيسية عاملة للتطوير وليست إعلانًا عن اكتمال نقل كل تفاصيل الموقع القديم. يجب مراجعة مطابقة جميع خصائص المصدر الأصلي، واختبار الرحلات القديمة والحقول المتخصصة وسير العمل بالكامل قبل النشر. الدفع الإلكتروني الفعلي، البريد الآلي، وإدارة صلاحيات متعددة المستويات ليست مفعلة بعد.

## Riferimento del sito originale
Il file `docs/reference/original-index.html` è una copia di riferimento non modificata del sorgente HTML originale ricevuto. Serve per completare la verifica di parità funzionale e il trasferimento delle funzioni storiche senza perdere la fonte originale.

## Recensioni e foto dei viaggiatori
- I visitatori possono inviare una recensione dalla sezione “Condividi i tuoi ricordi” in homepage.
- Il nome e il commento sono richiesti; le foto sono facoltative (fino a 5, PNG/JPG/WebP, massimo 3 MB ciascuna).
- Le recensioni restano in attesa e non appaiono pubblicamente finché un amministratore non le approva.
- Dalla gestione recensioni, l'amministratore può aprire le foto e contrassegnare manualmente una recensione come “Viaggiatore verificato” solo dopo aver verificato che la persona abbia effettivamente viaggiato con l'agenzia.
- Le foto caricate vengono salvate in `public/uploads`; includi questa cartella nei backup del progetto.

## Archivio clienti (CRM)

La versione finale include un archivio clienti nell'area amministratore. Non viene imposto un account/login agli ospiti: il cliente può chiedere disponibilità su WhatsApp senza compilare un profilo. Dopo averlo contattato e aver ottenuto il consenso, l'amministratore può registrare nome e almeno un recapito (WhatsApp o email), origine del contatto e note interne. I record sono salvati nella tabella `customers`; i campi `notes`, `source` e `consent` vengono aggiunti automaticamente anche ai database esistenti. L'API amministrativa consente inoltre di collegare una prenotazione a un profilo cliente tramite `PATCH /api/admin/bookings/:id/customer`.

Non inserire dati di clienti senza il loro consenso. Le recensioni e le immagini caricate dagli utenti restano soggette alla moderazione amministrativa già prevista; il contrassegno di cliente verificato va assegnato solo dopo aver verificato realmente l'esperienza.


## Aggiornamenti della versione dettagliata
- La pagina di ogni escursione mostra programma, punto di partenza, gruppo, lingue, inclusioni/esclusioni, punto d'incontro, cancellazione, note e mappa.
- Le recensioni approvate sono mostrate nella pagina della relativa escursione; i nuovi contributi restano in moderazione e possono includere fino a 5 foto.
- L'area amministrativa è divisa in schede: Dashboard, Escursioni, Recensioni, Impostazioni e Amministratori.
- È stato rimosso il modulo CRM che chiedeva all'amministratore di inserire manualmente i clienti.
- Le nuove colonne vengono aggiunte automaticamente al database esistente senza cancellare i dati già salvati.


### Customer reviews workflow
- Homepage displays only approved reviews explicitly selected by the administrator for the homepage. There is no review submission form on the homepage.
- Customers submit a review from the relevant excursion detail page and must confirm that they participated. Reviews remain pending until approved.
- In Admin → Recensioni, approve/reject reviews, verify a traveler, and choose whether an approved review appears on the homepage.
- General site copy is edited in Admin → Contenuti e impostazioni; excursion-specific information remains under Escursioni.
