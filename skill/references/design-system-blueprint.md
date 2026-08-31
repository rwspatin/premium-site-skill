# Design System Blueprint (PT-BR) — GRIGOLETTO

Tese: premium = menos decisões, repetidas com disciplina. Restrições → coesão → parece caro.

## 1. Paleta fechada
- Canvas: gradiente 180deg #FAFAF8 → #F4F3EF (nunca #FFFFFF chapado)
- Ink #16181D (nunca #000), ink soft #1D2027, cinza #8B8F98
- Borda: rgba(22,24,29,0.12) em claro; rgba(255,255,255,0.10) em escuro (hairline 1px)
- Acento único #2F6FED · hover #4C82FF · pressed #1E5AD6 — só links, CTA primário, focus ring. Nunca superfície.
- Superfície escura #0E0F12 / #16181D
- Teste em escala de cinza: hierarquia deve sobreviver sem cor.

## 2. Espaçamento (8pt)
space-1 8 · space-2 16 · space-3 24 · space-4 32 · space-5 48 · space-6 64. Nada entre eles.
Seção 64 vertical · card 24 · heading→body 16 · body→button 32 · icon→label 8.
Alinhamento óptico > matemático (deslocar 1–2px ícones/setas).

## 3. Tipografia (2 famílias)
Display serif (Fraunces) + body sans (Inter).
| Papel | px | lh | tracking | caixa |
| Label/chip | 12 | 1.3 | +1 | UPPER |
| Botão | 14 | 1.0 | +1.5 | UPPER |
| Nav | 16 | 1.4 | 0 | sentence |
| Body | 18 | 1.5 | 0 | sentence |
| Lead | 20 | 1.5 | 0 | sentence |
| H1 mobile | 28 | 1.1 | -0.25 | serif |
| H1 desktop | 56 | 0.95–1.0 | -0.5 | serif |
Line-height aperta conforme tamanho cresce; tracking inverso ao tamanho; uppercase só labels/botões.

## 4. Raios
input 8 · card 12 · card-lg/modal 16 · botão 24 · pill 999. Nunca 0.
Ordem para separar superfícies (escolha UMA): fill → shadow → border.

## 5. Sombras (tingidas na ink)
sm 0 1px 2px rgba(16,18,29,.06) · md 0 8px 24px .10 · lg 0 20px 50px .16
glow 0 0 30px rgba(47,111,237,.30) — no máximo 1 por tela. Em dark, hairline no lugar de sombra.

## 6. Componentes
Só var(--token); zero hex/px cru. 5 estados: rest/hover/focus/active/disabled.
CTA: ink pill, uppercase +1.5px, radius 24, padding 16 32, seta em acento, active scale(.97).
Chip: outline ink 1.5px, pill, hover preenche com acento. Card: paper, hairline, radius 12, padding 24, shadow-sm.

## 7. Motion
--ease-signature cubic-bezier(.16,1,.3,1) em tudo. --dur-ui .4s (.3–.5) · --dur-entrance 1.2s (1.0–1.4).
Reveal: opacity 0 + translateY 32px (24–40); stagger .1–.2s. Hover link: opacity .6 (não cor). Sem bounce/spring.
prefers-reduced-motion: neutralizar animações e revelar instantâneo.

## 8. :root canônico
```css
:root{
  --ink:#16181D; --paper:#FAFAF8; --accent:#2F6FED; --surface-dark:#0E0F12; --hairline:rgba(255,255,255,.10);
  --space-1:8px;--space-2:16px;--space-3:24px;--space-4:32px;--space-6:48px;--space-8:64px;
  --radius-sm:8px;--radius-md:12px;--radius-lg:16px;--radius-xl:24px;--radius-pill:999px;
  --font-display:"Fraunces",serif; --font-body:"Inter",sans-serif;
  --shadow-sm:0 1px 2px rgba(16,18,29,.06); --shadow-md:0 8px 24px rgba(16,18,29,.10);
  --shadow-lg:0 20px 50px rgba(16,18,29,.16); --shadow-glow:0 0 30px rgba(47,111,237,.30);
  --ease-signature:cubic-bezier(.16,1,.3,1); --dur-ui:.4s; --dur-entrance:1.2s;
}
```

## Checklist
- [ ] Canvas escolhido (não #FFF) · [ ] Ink suavizada (não #000) · [ ] 1 acento + variantes
- [ ] Rampa de cinzas · [ ] Escala 8pt · [ ] 2 fontes · [ ] Escala tipográfica fixa
- [ ] Família de raios · [ ] sm/md/lg + glow · [ ] Componentes core em tokens, 5 estados
- [ ] Easing único + reduced-motion · [ ] Tokens em um único :root
