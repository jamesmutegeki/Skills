<!--
  TURKISH EXPLANATION
  ───────────────
  This skill takes a vague or incomplete user request and transforms it into a clearer,
  more specific, more actionable prompt. It prevents the agent from
  working based on wrong assumptions. A vague request like "fix this" becomes
  "what condition is expected, what is the success criterion" format.

  WHEN: When the request is vague, scope is unclear, or multiple interpretations are possible.
            Runs before other skills — improves input quality.
  OUTPUT: Enhanced prompt text — goes directly to brainstorming or writing-plans.
-->

---
name: prompt-enhancer
description: >
  Takes a vague or incomplete user request and transforms it into a precise,
  actionable prompt. Identifies missing dimensions (what, why, who, success criteria,
  constraints) and either asks targeted questions or fills gaps with explicit assumptions.
  Runs before other skills to improve input quality.
---

# Prompt Enhancer Skill

## Philosophy

Answering the wrong question correctly is worse than answering the right question incorrectly.

The quality of a request determines the quality of its output. This skill prevents the agent from working in "I think this is what was meant" mode. It surfaces ambiguity and clarifies decision points.

---

## When to Trigger

This skill is triggered when any of these signals are present:

- İstek tek cümle ve detaysız: *"Login'i düzelt"*, *"Performansı artır"*, *"Daha iyi görünsün"*
- İstek birden fazla şekilde yorumlanabilir: *"Kullanıcı flow'unu iyileştir"*
- Hedef kitle veya bağlam yok: *"Bir dashboard ekle"*
- Başarı kriteri belirsiz: *"Hızlandır"*, *"Temizle"*, *"Optimize et"*
- Kapsam sınırı yok: *"Auth sistemini yenile"*

---

## Process

### Step 1: Identify Request Type

First, determine what type of request this is:

| Type | Example | Missing Dimensions |
|-----|-------|----------------|
| **Bug fix** | "Login çalışmıyor" | Hangi koşulda? Hata mesajı? Her zaman mı? |
| **Yeni özellik** | "Profil sayfası ekle" | Kim kullanacak? İçerik ne? Tasarım referansı? |
| **Refactor** | "Kodu temizle" | Hangi dosya/modül? Temizden kasıt ne? |
| **Performans** | "Hızlandır" | Hangi sayfa/endpoint? Hedef süre? Mevcut süre? |
| **Tasarım** | "Daha güzel yap" | Referans var mı? Hangi element? Hangi bağlam? |
| **Altyapı** | "Deploy otomatikleştir" | Hangi ortam? Mevcut pipeline? Tetikleyici ne? |

---

### Step 2: Find Missing Dimensions

For each request, check these 6 dimensions. Mark the missing ones:

```
[ ] WHAT   — What exactly will change/be added?
[ ] WHY    — Why is it needed? What problem does it solve?
[ ] WHO    — Who will use it? Which user/system?
[ ] WHEN   — Is there a deadline? In what order?
[ ] HOW    — Are there constraints? (stack, platform, budget, existing API)
[ ] DONE   — What is the success criterion? When can we say "Done"?
```

---

### Step 3: Choose Enhancement Strategy

**Strateji A — Sor** (1-2 kritik belirsizlik varsa):

Only ask the most important questions. State why each question is asked:

```
I have 2 questions — I'll choose a different approach based on the answers:

1. When you say "Login doesn't work", under what conditions?
   a) Always?
   b) In a specific browser?
   c) For a specific user?
   (This determines the bug's scope)

2. Is there an existing error message or log?
   (If you share it, root cause analysis will be much faster)
```

**Strateji B — Varsay ve Göster** (3+ belirsizlik varsa, hızlı ilerleme öncelikli):

Make reasonable assumptions, list them explicitly, and produce the enhanced prompt:

```
İstek: "Profil sayfası ekle"

Varsayımlar (onaylamanı bekliyorum):
- Kullanıcı: giriş yapmış kayıtlı kullanıcılar
- İçerik: isim, email, avatar, bio
- Tasarım: mevcut design system'e uygun
- Auth: JWT token ile korumalı route
- Kapsam: frontend + backend endpoint

Bu varsayımlardan farklı bir şey var mı? Yoksa bu scope ile başlayalım.
```

---

### Step 4: Produce Enhanced Prompt

The final output format is the same for both strategies:

```markdown
## Enhanced Prompt

**Original Request:** <kullanıcının orijinal isteği>

**Clarified Task:**
<spesifik, tek yorumlu açıklama>

**In Scope:**
- <madde>
- <madde>

**Out of Scope:**
- <madde> (kapsam dışı bırakma gerekçesi)

**Success Criteria:**
1. <ölçülebilir kriter>
2. <ölçülebilir kriter>

**Technical Constraints:**
- <stack, platform, bağımlılık>

**Open Questions:**
- <varsa — bunlar ilerleyen aşamada cevaplanacak>

**Recommended Next Skill:**
→ <brainstorming | writing-plans | test-driven-execution>
```

---

### Step 5: Get User Confirmation

Show the enhanced prompt to the user:

```
I understand it this way — is this correct?

[Enhanced Prompt içeriği]

If you approve, I can start. Is there anything you want to change?
```

No proceeding to next skill without confirmation.

---

## Examples

### Example 1: Bug Fix

**Kullanıcı:** *"Şifre sıfırlama çalışmıyor"*

**Analysis:**
- WHAT: password reset — but at which step?
- HOW: no error message
- WHO: all users or specific one?

**Strategy A — Ask:**
```
Which step of the "password reset" flow has the problem?
  a) Email not being sent?
  b) Error when clicking the link?
  c) New password not being saved?
```

---

### Example 2: Performance

**Kullanıcı:** *"Sayfa çok yavaş"*

**Analysis:** 6 boyutun 5'i eksik.

**Strategy B — Assume and Show:**
```
Let me proceed with these assumptions:
- Which page: /dashboard (most used)
- Current state: 4-5 seconds TTI (estimate)
- Target: under 2 seconds
- Platform: web (not mobile)
- Constraint: current stack unchanged

If different, let me know, otherwise I'll start by pulling Lighthouse profile.
```

---

## Rules

- **Always get confirmation.** Enhanced prompt kullanıcı onayı olmadan uygulanmaz.
- **Ask maximum 3 questions.** Daha fazlası kullanıcıyı yorar — en kritik 3'ü seç.
- **Don't hide assumptions.** Her varsayım açıkça listelenir.
- **Also state what's out of scope.** "Bunu yapmıyorum çünkü" = en değerli netlik.
- **Suggest the next skill.** Enhanced prompt'tan sonra hangi skill çalışacak belirt.
