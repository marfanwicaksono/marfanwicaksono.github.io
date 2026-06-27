# Panduan Pengisian Data Portfolio

Dokumen ini berisi aturan pengisian file JSON di folder data agar konsisten dengan frontend, filter tag, dan generator resume.

## Daftar File

- [personal.json](personal.json)
- [skills.json](skills.json)
- [projects.json](projects.json)
- [experience.json](experience.json)
- [education.json](education.json)
- [publications.json](publications.json)
- [research.json](research.json)
- [awards.json](awards.json)
- [training.json](training.json)

## Aturan Umum

1. Semua file wajib valid JSON (pakai double quote, tanpa trailing comma).
2. Semua id harus unik dalam 1 file.
3. Gunakan prefix id sesuai jenis data:
- Experience: exp-<angka>
- Education: edu-<angka>
- Certification: cert-<slug/tahun>
- Project: project-<angka>
- Publication: pub-<angka>
- Patent: patent-<angka>
- Research: res-<angka>
- Award: award-<angka>
- Organization: org-<angka>
- Committee: com-<angka>
- Training: train-<angka>
4. Field tags harus berupa array string, contoh: ["AI & Machine Learning", "Education & Training"].
5. Jika item tidak ingin ditampilkan, tambahkan visible: false.

## Aturan Tanggal

Gunakan format berikut:

- date: YYYY-MM (contoh: 2026-05)
- startDate / endDate: YYYY-MM
- year: YYYY (khusus field year di research dan beberapa committee lama)

Untuk data rentang waktu:

- Jika ongoing: isi startDate, set current: true, dan endDate boleh null atau dihilangkan.
- Jika selesai: isi startDate dan endDate.
- Hindari nilai teks seperti "present" pada field tanggal.

## Tag yang Disarankan

Tag bebas, tetapi agar filter rapi sebaiknya gunakan label yang sudah umum di project ini:

- AI & Machine Learning
- Data Science
- IoT & Embedded
- Networking
- Software Engineering
- Web Development
- Cloud & Infrastructure
- Multimedia
- Research
- Education & Training
- Project Management

## Aturan Per File

### [personal.json](personal.json)

Field utama:

- name (string, wajib)
- title (string, wajib)
- tagline (string, wajib)
- bio (string, wajib)
- socialLinks (object, wajib)

Field umum lain:

- avatar, resumeUrl, siteLogo, description, url, ogImage, twitterHandle, whatsappNumber, whatsappDefaultMessage, location

Catatan:

- socialLinks minimal disarankan berisi email dan linkedin/github.
- whatsappNumber gunakan format nomor internasional tanpa tanda +.

### [skills.json](skills.json)

Struktur:

- categories: array
- categories[].id (wajib)
- categories[].name (wajib)
- categories[].skills: array

Field skill umum:

- name (wajib)
- icon, experience, expertise, capabilities, tags (opsional)
- relatedExperience, relatedProjects, relatedPublications, relatedResearch, relatedTraining, relatedAwards, relatedEducation (opsional, array id referensi)

Catatan penting relasi:

- Semua id relasi harus mengarah ke id yang benar-benar ada di file data lain.

### [projects.json](projects.json)

Field project:

- id, title, description, expertise (wajib)
- longDescription, image, liveUrl, githubUrl, organization, category, tags, featured, visible (opsional)
- startDate, endDate (opsional, format YYYY-MM)

Catatan:

- Jika startDate dipakai, endDate sebaiknya juga dipakai agar rentang tanggal tampil konsisten.
- Untuk project ongoing, lebih aman kosongkan endDate daripada mengisi string non-tanggal.

### [experience.json](experience.json)

Field experience:

- id, company, position, startDate, description (wajib)
- location, type, endDate, current, achievements, expertise, tags, visible (opsional)

Nilai type yang dipakai:

- full-time
- part-time
- contract
- freelance
- internship

### [education.json](education.json)

Memiliki 2 group:

- education
- certifications

Field education:

- id, institution, degree, field, startDate (wajib)
- location, endDate, gpa, achievements, tags, visible (opsional)

Field certification:

- id, name, issuer, date (wajib)
- credentialUrl, credentialId, expiryDate, tags, visible (opsional)

### [publications.json](publications.json)

Memiliki 2 group:

- publications
- patents

Field publication:

- id, title, journal, date, type, authors (wajib)
- url, tags, visible (opsional)

Nilai type publication:

- journal
- conference

Field patent:

- id, title, type, issuer, date (wajib)
- tags, visible (opsional)

### [research.json](research.json)

Field research:

- id, title, funder, status, description (wajib)
- year atau startDate/endDate/current (minimal salah satu model tanggal dipakai)
- tags, visible (opsional)

Nilai status:

- ongoing
- completed

Saran:

- Gunakan startDate/endDate/current untuk data baru.
- year tetap boleh dipakai untuk kompatibilitas data lama.

### [awards.json](awards.json)

Memiliki 3 group:

- awards
- organizations
- committee

Field awards:

- id, title, event, organizer, level, date, description (wajib)
- tags, visible (opsional)

Field organizations:

- id, position, organization, startDate, endDate (wajib)
- tags, visible (opsional)

Field committee:

- id, position, event, organization, date (wajib)
- tags, visible (opsional)

### [training.json](training.json)

Group:

- trainer

Field training:

- id, title, organization, type (wajib)
- date atau startDate (minimal salah satu wajib)
- endDate, current, tags, visible (opsional)

Nilai type training:

- trainer
- speaker
- mentor
- instructor
- moderator

Saran:

- Data single event: pakai date.
- Data periode: pakai startDate + endDate, atau startDate + current: true.

## Contoh Template Item Baru

Contoh experience baru:

```json
{
  "id": "exp-8",
  "company": "Nama Perusahaan",
  "position": "Posisi",
  "location": "Bandung, Indonesia",
  "type": "full-time",
  "startDate": "2026-01",
  "endDate": null,
  "current": true,
  "description": "Ringkasan peran.",
  "achievements": [
    "Pencapaian 1",
    "Pencapaian 2"
  ],
  "expertise": ["Python", "Machine Learning"],
  "tags": ["AI & Machine Learning", "Software Engineering"]
}
```

Contoh training periode:

```json
{
  "id": "train-28",
  "title": "IoT Mentor",
  "organization": "Contoh Program",
  "startDate": "2026-03",
  "endDate": "2026-05",
  "type": "mentor",
  "tags": ["IoT & Embedded", "Education & Training"]
}
```

## Checklist Sebelum Commit

1. JSON valid dan tidak ada typo key.
2. id baru unik.
3. format tanggal sesuai aturan.
4. tags konsisten (ejaan sama).
5. relasi id di skills.json mengarah ke id yang valid.
6. Jalankan cek cepat di UI lokal setelah perubahan data.
