Este é o meu arquivo de models para criação de memorial

Eu preciso de colocar data de criação e atulização em vários campos para log, qual é a melhor forma de fazer isto?

Onde preciso de log:

Na criação e atualização:
- Do proprio memorial
- Do epitáfio
- Tributo
- Lifestory
- Stories
- Gallery
Segue o arquivo:
const mongoose = require("mongoose")

const MemorialSchema = new mongoose.Schema({
  user: { type: mongoose.Schema.Types.ObjectId, ref: "User", required: false },
  firstName: { type: String, required: true }, // Obrigatório
  lastName: { type: String, required: true }, // Obrigatório
  slug: { type: String, unique: true, required: true }, // Campo slug único e obrigatório
  gender: { type: String, required: false },
  relationship: { type: String, required: false },
  birth: {
    date: { type: Date, required: false },
    city: { type: String },
    state: { type: String },
    country: { type: String },
  },
  death: {
    date: { type: Date, required: false },
    city: { type: String },
    state: { type: String },
    country: { type: String },
  },
  epitaph: { type: String, maxlength: 255 }, // Novo campo Epitáfio
  about: { type: String },
  tribute: [{ name: String, message: String }],
  lifeStory: [
    {
      title: { type: String, default: "" }, // Título opcional
      content: { type: String, default: "" }, // Conteúdo opcional
    },
  ],
  stories: [
    {
      title: { type: String, default: "" }, // Título opcional
      content: { type: String, default: "" }, // Conteúdo opcional
    },
  ],
  gallery: {
    photos: [{ type: String }], // URLs das imagens
    audios: [{ type: String }], // URLs dos áudios
    videos: [{ type: String }], // URLs dos vídeos
  },
  theme: { type: String, default: "blue-theme" },
})

module.exports = mongoose.model("Memorial", MemorialSchema)
***************************************************************
---
Para adicionar datas de criação e atualização em vários campos para log, você pode utilizar os recursos do Mongoose, como `timestamps` e `middleware` (também conhecido como "hooks"). Vou te mostrar como fazer isso de forma eficiente.

### 1. **Adicionar `timestamps` ao modelo principal (`Memorial`)**

O Mongoose permite adicionar automaticamente campos `createdAt` e `updatedAt` ao seu esquema principal. Basta passar a opção `timestamps: true` no esquema:

```javascript
const MemorialSchema = new mongoose.Schema(
  {
    user: { type: mongoose.Schema.Types.ObjectId, ref: "User", required: false },
    firstName: { type: String, required: true },
    lastName: { type: String, required: true },
    slug: { type: String, unique: true, required: true },
    gender: { type: String, required: false },
    relationship: { type: String, required: false },
    birth: {
      date: { type: Date, required: false },
      city: { type: String },
      state: { type: String },
      country: { type: String },
    },
    death: {
      date: { type: Date, required: false },
      city: { type: String },
      state: { type: String },
      country: { type: String },
    },
    epitaph: { type: String, maxlength: 255 },
    about: { type: String },
    tribute: [{ name: String, message: String }],
    lifeStory: [
      {
        title: { type: String, default: "" },
        content: { type: String, default: "" },
      },
    ],
    stories: [
      {
        title: { type: String, default: "" },
        content: { type: String, default: "" },
      },
    ],
    gallery: {
      photos: [{ type: String }],
      audios: [{ type: String }],
      videos: [{ type: String }],
    },
    theme: { type: String, default: "blue-theme" },
  },
  { timestamps: true } // Adiciona createdAt e updatedAt automaticamente
);
```

Isso adicionará automaticamente os campos `createdAt` e `updatedAt` ao modelo `Memorial`.

---

### 2. **Adicionar `timestamps` a subdocumentos (como `tribute`, `lifeStory`, `stories`, etc.)**

Para subdocumentos, você pode adicionar manualmente os campos `createdAt` e `updatedAt` e usar `middleware` para atualizá-los.

Por exemplo, para o campo `tribute`:

```javascript
const TributeSchema = new mongoose.Schema({
  name: { type: String, required: true },
  message: { type: String, required: true },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now },
});

const MemorialSchema = new mongoose.Schema(
  {
    // ... outros campos
    tribute: [TributeSchema],
  },
  { timestamps: true }
);
```

Para atualizar automaticamente o campo `updatedAt` quando um tributo for modificado, você pode usar um `pre` hook:

```javascript
MemorialSchema.pre("save", function (next) {
  if (this.isModified("tribute")) {
    this.tribute.forEach((tribute) => {
      tribute.updatedAt = Date.now();
    });
  }
  next();
});
```

Repita o mesmo processo para `lifeStory`, `stories`, etc.

---

### 3. **Adicionar `timestamps` ao campo `gallery`**

Para o campo `gallery`, você pode adicionar `createdAt` e `updatedAt` a cada item da galeria (fotos, áudios, vídeos). Por exemplo:

```javascript
const GalleryItemSchema = new mongoose.Schema({
  url: { type: String, required: true },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now },
});

const GallerySchema = new mongoose.Schema({
  photos: [GalleryItemSchema],
  audios: [GalleryItemSchema],
  videos: [GalleryItemSchema],
});

const MemorialSchema = new mongoose.Schema(
  {
    // ... outros campos
    gallery: GallerySchema,
  },
  { timestamps: true }
);
```

E use um `pre` hook para atualizar `updatedAt` quando a galeria for modificada:

```javascript
MemorialSchema.pre("save", function (next) {
  if (this.isModified("gallery")) {
    this.gallery.photos.forEach((photo) => (photo.updatedAt = Date.now()));
    this.gallery.audios.forEach((audio) => (audio.updatedAt = Date.now()));
    this.gallery.videos.forEach((video) => (video.updatedAt = Date.now()));
  }
  next();
});
```

---

### 4. **Resumo das alterações**

- Adicione `timestamps: true` ao esquema principal para `createdAt` e `updatedAt`.
- Para subdocumentos (`tribute`, `lifeStory`, `stories`, etc.), adicione manualmente `createdAt` e `updatedAt`.
- Use `pre` hooks para atualizar automaticamente o campo `updatedAt` quando os subdocumentos forem modificados.

---

### 5. **Exemplo completo**

Aqui está o arquivo completo com todas as alterações:

```javascript
const mongoose = require("mongoose");

const TributeSchema = new mongoose.Schema({
  name: { type: String, required: true },
  message: { type: String, required: true },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now },
});

const LifeStorySchema = new mongoose.Schema({
  title: { type: String, default: "" },
  content: { type: String, default: "" },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now },
});

const StorySchema = new mongoose.Schema({
  title: { type: String, default: "" },
  content: { type: String, default: "" },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now },
});

const GalleryItemSchema = new mongoose.Schema({
  url: { type: String, required: true },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now },
});

const GallerySchema = new mongoose.Schema({
  photos: [GalleryItemSchema],
  audios: [GalleryItemSchema],
  videos: [GalleryItemSchema],
});

const MemorialSchema = new mongoose.Schema(
  {
    user: { type: mongoose.Schema.Types.ObjectId, ref: "User", required: false },
    firstName: { type: String, required: true },
    lastName: { type: String, required: true },
    slug: { type: String, unique: true, required: true },
    gender: { type: String, required: false },
    relationship: { type: String, required: false },
    birth: {
      date: { type: Date, required: false },
      city: { type: String },
      state: { type: String },
      country: { type: String },
    },
    death: {
      date: { type: Date, required: false },
      city: { type: String },
      state: { type: String },
      country: { type: String },
    },
    epitaph: { type: String, maxlength: 255 },
    about: { type: String },
    tribute: [TributeSchema],
    lifeStory: [LifeStorySchema],
    stories: [StorySchema],
    gallery: GallerySchema,
    theme: { type: String, default: "blue-theme" },
  },
  { timestamps: true }
);

// Middleware para atualizar updatedAt em subdocumentos
MemorialSchema.pre("save", function (next) {
  if (this.isModified("tribute")) {
    this.tribute.forEach((tribute) => (tribute.updatedAt = Date.now()));
  }
  if (this.isModified("lifeStory")) {
    this.lifeStory.forEach((story) => (story.updatedAt = Date.now()));
  }
  if (this.isModified("stories")) {
    this.stories.forEach((story) => (story.updatedAt = Date.now()));
  }
  if (this.isModified("gallery")) {
    this.gallery.photos.forEach((photo) => (photo.updatedAt = Date.now()));
    this.gallery.audios.forEach((audio) => (audio.updatedAt = Date.now()));
    this.gallery.videos.forEach((video) => (video.updatedAt = Date.now()));
  }
  next();
});

module.exports = mongoose.model("Memorial", MemorialSchema);
```

Com isso, você terá um sistema de log completo para criação e atualização em todos os campos necessários.
