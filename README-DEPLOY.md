# README-DEPLOY — SL Excellence Creative Studio

Landing page **single-file** (`index.html`) du studio UGC hôtellerie de luxe :
cyber-luxe noir abyssal + or 24K, moteur scrollytelling conservé, contenu 100 % anglais,
commentaires de code en français.

- **Fichier du site :** `index.html` (un seul fichier, aucune build step)
- **Assets :** `assets/bg-studio.jpg` (fond fixe du site) et `assets/svc-01/02/03/06/07/08.jpg`
  (visuels des cartes services, 1200×675). Les deux fichiers sont remplaçables à l'identique.
- **Cible :** `https://creator.sl-excellence.com`
- **Dépendances externes (CDN uniquement) :** Tailwind Play CDN, Feather Icons, Google Fonts (Montserrat),
  GSAP + ScrollTrigger + Lenis (couche d'animations v2), SDK web Vapi (chargé uniquement si configuré),
  embed Cal.com (chargé uniquement si configuré). Aucun React, aucun bundler, aucune build step.

---

## 0. Nouveautés v2 (résumé)

- **Cartes services :** chaque carte de `#services` a désormais une bande média en haut de carte
  (`assets/svc-01/02/03/06/07/08.jpg`). Les cartes 04 et 05, sans photo disponible, affichent un
  panneau graphique doré avec icône. Pour changer un visuel : remplacer le fichier ou le `src`
  dans l'`<article class="service-card">` correspondant.
- **Portfolio :** les cartes sans vidéo (`video: null`) affichent une **affiche typographique dorée**
  (catégorie en haut, titre centré, badge « In production » en bas) — plus aucune case noire.
  Un clic n'ouvre jamais de lightbox vide : il affiche le message discret « In production — coming soon ».
- **Offres :** la 3e carte devient **Signature Partnership** (`from $1,900 / month` — un jour de production
  par saison, soit 4 par an, plus un drop mensuel de 4 vidéos). Les packs 1 et 2 sont inchangés.
- **Contact & réseaux :** `contact@sl-excellence.com`, Instagram `sl_excellence`, TikTok `@nypsuss`
  (logo SVG officiel au lieu de l'icône Feather), YouTube retiré. Il ne reste **aucun `href="#"`** dans la page.
- **Bandeau défilant :** masque en fondu à gauche (le rail « Explore Network » reste lisible) et à droite.
- **Couche d'animations « nouvelle génération » :** GSAP + ScrollTrigger + Lenis via CDN — en-têtes de
  section animés, grilles en cascade, parallaxe douce, tilt 3D des cartes, boutons magnétiques, barre de
  progression de page, ancres fluides, ligne du process qui se remplit. Elle se branche sur l'événement
  `slx:hero-released` émis par le moteur du hero (qui n'est **jamais** modifié) et respecte
  `prefers-reduced-motion` (si l'utilisateur réduit les animations, rien n'est initialisé).

---

## 1. Déploiement

### Option A — Netlify, glisser-déposer (le plus rapide)

1. Aller sur <https://app.netlify.com/drop>.
2. Glisser le dossier `sl-excellence-creator` (il doit contenir `index.html`).
3. Netlify fournit une URL en `*.netlify.app` → la tester.
4. Pour le domaine final : **Site settings → Domain management → Add custom domain** →
   `creator.sl-excellence.com`, puis créer l'enregistrement DNS demandé par Netlify
   (CNAME vers `<nom-du-site>.netlify.app`).
5. HTTPS est provisionné automatiquement (Let's Encrypt).

> Le glisser-déposer écrase le site précédent à chaque envoi : pratique pour une mise à jour rapide.

### Option B — Netlify via GitHub (déploiement continu)

1. Pousser le repo sur GitHub (⚠️ ne pas exécuter `git push` depuis la machine de build si vous n'êtes pas
   autorisé à publier — le dépôt peut aussi être envoyé manuellement).
2. Sur Netlify : **Add new site → Import an existing project → GitHub** → sélectionner le repo.
3. Réglages de build :
   - **Build command :** *(laisser vide)*
   - **Publish directory :** `.`
4. **Deploy site**. Chaque `git push` sur la branche déploie automatiquement.

### Option C — Cloudflare Pages

1. <https://dash.cloudflare.com> → **Workers & Pages → Create → Pages → Connect to Git**.
2. Sélectionner le repo, puis :
   - **Framework preset :** `None`
   - **Build command :** *(vide)*
   - **Build output directory :** `/` (racine du repo)
3. **Save and Deploy**. Domaine personnalisé : **Custom domains → Set up a custom domain** →
   `creator.sl-excellence.com` (le domaine est déjà chez Cloudflare dans la plupart des cas, l'enregistrement
   se fait en un clic).

> Dans les deux cas, aucun fichier de config n'est nécessaire : le site est un `index.html` statique.

### Mise à jour du site

Remplacer `index.html` par la nouvelle version (glisser-déposer ou `git push`), rien d'autre à faire.
Les balises anti-cache sont déjà présentes dans le `<head>`.

---

## 2. Remplir les clés et les liens — `SLX_CONFIG`

Tout se passe dans **un seul bloc**, en haut du fichier (chercher `window.SLX_CONFIG`) :

```js
window.SLX_CONFIG = {
    email: "contact@sl-excellence.com",            // adresse de contact du studio
    whatsapp: "34602199293",                      // lien wa.me
    instagram: "https://www.instagram.com/sl_excellence/", // URL complète du profil
    elevenLabsApiKey: "",                         // optionnel — voix premium ElevenLabs (proxifier en prod)
    elevenVoiceIds: { en: "", fr: "", es: "" },   // TODO: IDs de voix
    voiceDemoUrls: { en: "", fr: "", es: "" },    // recommandé — MP3 de tes vraies voix (prioritaire sur tout)
    vapiPublicKey: "",                            // TODO
    vapiAssistantId: "",                          // TODO
    calLink: ""                                   // TODO: ex "sl-excellence/discovery"
};
```

| Champ | Effet | Où le trouver |
|---|---|---|
| `email` | Liens « Email » (section Contact + panneau Concierge) et repli `mailto:` du formulaire | votre boîte studio |
| `whatsapp` | Liens WhatsApp (footer, contact, concierge) au format international **sans `+`** | votre numéro |
| `instagram` | Liens Instagram (footer + contact). **URL complète** (ex. `https://www.instagram.com/monprofil/`) ; vide = lien `#` | URL du profil |
| `elevenLabsApiKey` | **Optionnel** — voix premium à la volée. Sans clé : voix système gratuites (EN/FR/ES) | dashboard ElevenLabs |
| `elevenVoiceIds` | IDs des 3 voix (EN/FR/ES) | dashboard ElevenLabs → Voices |
| `voiceDemoUrls` | **Prioritaire** : 3 MP3 pré-générés à servir depuis votre hébergement | vos fichiers |
| `vapiPublicKey` / `vapiAssistantId` | Active le concierge vocal IA (bouton flottant bas droite) | dashboard Vapi |
| `calLink` | Active l'embed Cal.com dans la section Contact | Cal.com → Event type |

**Rien n'est jamais « mort » :** sans clé, le site reste 100 % fonctionnel —
le lecteur de voix utilise les *voix système gratuites du navigateur* (EN/FR/ES),
et le concierge affiche les canaux directs.

---

## 3. Remplacer les vidéos du portfolio

Le portfolio est **data-driven** : tout est dans le tableau `PORTFOLIO` (script « PORTFOLIO », milieu du fichier).

```js
{
    title: 'Above the Coastline',                 // titre affiché
    hook: "The coastline you've been scrolling for.", // accroche
    category: 'Travel & Destinations',            // tag + filtre
    meta: 'Destination reel · 9:16',              // ligne de méta
    video: 'https://…/mon-fichier.mp4',           // URL directe du MP4 (ou null)
    poster: ''                                    // optionnel : image d'affiche
}
```

- **Vidéo réelle** → renseigner `video` (MP4 direct, IPFS, Cloudflare Stream, Bunny, etc.).
  La carte lit la vidéo **au survol** (desktop) et le **clic ouvre la lightbox** avec son.
- **Carte affiche** (fond or / bleu nuit, lignes diagonales + catégorie, titre, badge « In production »)
  → mettre `video: null`. Au clic, un message discret s'affiche (jamais de lightbox vide).
  Les 4 cartes affiche portent le commentaire `// TODO: remplacer par une vidéo réelle`.
- **`poster`** : URL d'une image d'affiche (optionnelle) affichée avant la lecture.

**Format conseillé :** vertical 9:16, 1080×1920, H.264 MP4, < 10 Mo par vidéo (hébergement externe conseillé
pour ne pas alourdir le repo).

### Catégories et filtres

Filtres affichés : `All` · `Hospitality` · `Travel & Destinations` · `Lifestyle` · `Food & Dining` · `Products`.

Le filtrage se fait sur la propriété `category`. Pour rattacher une carte à plusieurs filtres, ajouter
`filterTags: ['MaCatégorie', 'AutreFiltre']` (déjà utilisé par `Sunrise Ritual`, catégorie « Experiences »,
rattachée au filtre « Lifestyle » car aucun onglet « Experiences » n'est prévu).

---

## 4. Lecteur multilingue (voix) — 3 chemins de lecture

Au clic sur 🇬🇧 / 🇫🇷 / 🇪🇸 :

1. **`voiceDemoUrls[lang]` renseigné** → lecture du MP3 pré-généré (chemin recommandé en production :
   tes vraies voix, aucun coût d'API, aucune clé exposée).
2. Sinon, **`elevenLabsApiKey` + `elevenVoiceIds[lang]` renseignés** → appel direct
   `POST https://api.elevenlabs.io/v1/text-to-speech/{voiceId}/stream` avec l'en-tête `xi-api-key`,
   réponse `audio/mpeg` jouée via un Blob (voix premium ElevenLabs).
3. Sinon → **voix système gratuites du navigateur** (`speechSynthesis` : une voix native par langue,
   aucun compte requis) + visualiseur animé ; si aucune voix n'existe : oscillateur discret en dernier
   recours. La vidéo du téléphone ne se fige jamais.

> 💡 **Sans aucune clé**, la démo vocale fonctionne déjà en EN/FR/ES (voix système, qualité selon
> l'appareil). Pour des voix premium : enregistre tes propres MP3 — tu es trilingue, c'est ton
> meilleur atout — et pointe-les dans `voiceDemoUrls`.

> ⚠️ **Sécurité — chemin 2 :** appeler l'API ElevenLabs depuis le navigateur expose la clé API à tous les
> visiteurs. C'est acceptable **uniquement pour une démo**. En production, générez les MP3 en amont
> (chemin 1) ou proxifiez l'appel via une fonction serverless
> (Netlify Functions / Cloudflare Workers) qui garde la clé côté serveur.

---

## 5. Formulaire de devis (Netlify Forms)

Le formulaire est déjà câblé :

- `name="quote"`, `data-netlify="true"`, honeypot `bot-field`, champ caché `form-name`.
- Champs : `name`, `company`, `email`, `country`, `project_type`, `message`.

**Activation :**

1. Déployer sur **Netlify** (les Netlify Forms ne fonctionnent que là).
2. Une fois le site déployé, aller dans **Site → Forms** : le formulaire `quote` doit apparaître
   après la première visite de la page.
3. Ajouter une notification : **Forms → quote → Settings → Form notifications → Add notification → Email**
   → votre adresse (sinon les demandes restent uniquement dans le dashboard).

**Comportement de repli :** hors Netlify (test local, Cloudflare Pages, ou échec réseau), le JS ouvre un
`mailto:` pré-rempli avec les champs saisis, puis affiche un message de succès doré.
Sur un site statique non-Netlify, prévoir un endpoint (Formspree, Web3Forms, Worker) si vous voulez un vrai
envoi serveur.

---

## 6. Concierge vocal (Vapi)

- Si `vapiPublicKey` **et** `vapiAssistantId` sont renseignés : le SDK web officiel est chargé à la demande
  (`https://cdn.jsdelivr.net/npm/@vapi-ai/web@2/+esm`, alternative officielle documentée :
  `https://cdn.jsdelivr.net/gh/VapiAI/html-script-tag@latest/dist/assets/index.js`).
  Le panneau « Studio Call » gère les états `idle → connecting → live` (indicateur d'onde, mute, raccrocher)
  et transmet à l'assistant : langue du navigateur, date du jour, objectif choisi
  (`Hotel partnership` / `Paid ads quote`).
- Sinon : le bouton ouvre un panneau avec les **canaux directs** (WhatsApp / email / devis) — jamais de bouton mort.

## 7. Réservation (Cal.com)

- Renseigner `calLink` au format `utilisateur/event-slug` (ex. `sl-excellence/discovery`).
- L'embed inline (thème sombre) est injecté dans `#cal-embed` via le snippet officiel
  `https://app.cal.com/embed/embed.js`.
- Si `calLink` est vide, une carte de repli stylée s'affiche à la place (aucun trou visuel).

---

## 8. Structure de la page (ordre des sections)

`#hero-fixed-stage` (hero 3 cartes, conservé) → bandeau défilant → `#work` (portfolio, 6 cartes) →
`#services` (8 capacités) → `#sound` (voix EN/FR/ES) → `#process` (5 étapes) → `#studio` →
`#packages` (3 offres) → `#faq` (6 questions) → `#contact` (formulaire + Cal.com) → footer.

Menu latéral : `#nav-trigger` (desktop) / bouton hamburger (mobile), liens `Work · Services · Sound ·
Packages · Studio · Contact` + bloc discret « SL Excellence Network ».
Sélecteur de langue (Google Translate) : globe en haut à droite (desktop), globe dans le menu (mobile),
et ligne discrète en pied de page.

---

## 9. Checklist avant mise en ligne

- [ ] `email` réel renseigné (sinon les boutons Email ouvrent une adresse fictive).
- [ ] `instagram` renseigné (footer + contact) — déjà pré-rempli avec `sl_excellence` ; lien **TikTok**
      (`@nypsuss`) à confirmer. YouTube a été retiré du footer.
- [ ] Vidéos 3 à 6 du portfolio remplacées (`// TODO: remplacer par une vidéo réelle`).
- [ ] `voiceDemoUrls` (ou clé ElevenLabs pour la démo) + `elevenVoiceIds` renseignés.
- [ ] `vapiPublicKey` / `vapiAssistantId` renseignés (sinon le concierge reste en mode canaux directs).
- [ ] `calLink` renseigné (sinon carte de repli).
- [ ] Netlify Forms activé + notification e-mail configurée.
- [ ] Tester sur mobile : hero (3 paliers au scroll), filtres du portfolio, lightbox, formulaire.

---

## 10. Notes techniques

- **Moteur hero :** machine à paliers conservée (IDs/classes/fonctions d'origine). Tant que le hero n'est pas
  terminé, `<html>` porte la classe `hero-lock` (barre de défilement verrouillée) ; à la libération, le moteur
  retire cette classe, émet l'événement `slx:hero-released` (détail `{target:'#work'}`) puis laisse la main.
  La couche d'animations écoute cet événement : elle démarre Lenis et prend le relais du défilement
  (si elle n'est pas chargée, le moteur applique son scroll natif de secours).
- **Audio hero :** muet par défaut ; déverrouillage au premier clic/touch ; en cas de refus navigateur le
  script reste muet **sans jamais figer** la vidéo.
- **Motion :** `prefers-reduced-motion` coupe le bandeau défilant, les shimmers et les animations non
  essentielles — et **désactive complètement** la couche GSAP/Lenis (le site reste alors en défilement natif).
- **Défilement lissé :** Lenis est mis en pause pendant l'ouverture du menu latéral et de la lightbox
  portfolio, puis relancé à la fermeture. Les liens d'ancre (`#work`, `#contact`…) sont lissés par Lenis ;
  sans Lenis (CDN bloqué), le comportement natif du navigateur s'applique.
- **Accessibilité :** focus visibles (liseré or), cibles tactiles ≥ 44 px, navigation clavier
  (Échap ferme menu / lightbox / panneau concierge, Entrée/Espace ouvre une carte du portfolio).
- **Performance :** vidéos en `preload="metadata"` + lecture au survol, SDK tiers chargés uniquement si
  configurés. Pour aller plus loin : servir les MP4 via Cloudflare Stream / Bunny et ajouter des posters.
