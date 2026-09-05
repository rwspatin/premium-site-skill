# Selo RWS — maker's mark

Marca de construtor de Renan W. Spatin ("Se útil, deixe rastro."). Fleur-de-lis + RWS em
Fraunces (opsz 144, wght 420, tracking 0.18em) convertida em **paths puros** — nenhuma
dependência de fonte. Herda cor via `currentColor`.

- `seal.svg` — lockup horizontal (fleur + RWS). Uso padrão em footers/documentos.
- `seal-mark.svg` — só a fleur (favicons, espaços mínimos).

## Regras de uso
1. Sempre o arquivo canônico deste diretório — nunca redesenhar/aproximar.
2. Cores: brass `#c5a059` sobre escuro · ink `#1a1c1a` sobre claro · ou `currentColor`.
3. Tamanho mínimo: 72px de largura (lockup) / 12px (mark). Área de respiro ≥ altura da fleur/2.
4. **Onde aplicar**: projetos próprios e voluntários, documentos de consultoria, camadas
   invisíveis (meta author, humans.txt, console). **Nunca** em produto de cliente/empregador
   sem permissão expressa.

## Snippets

Footer (HTML/JSX — inline pra herdar cor):
```html
<a href="https://renanspatin.com" target="_blank" rel="noopener author"
   title="Feito por Renan W. Spatin" style="display:inline-flex;align-items:center;opacity:.55">
  <!-- colar o conteúdo de seal.svg aqui, com height:14px;width:auto -->
</a>
```

Console (uma linha, em qualquer entry point):
```js
console.log("%c✢ RWS", "color:#c5a059;font-size:14px", "— feito por Renan W. Spatin · renanspatin.com");
```

humans.txt (servir em /humans.txt):
```
/* TEAM */
Builder: Renan W. Spatin
Site: renanspatin.com
Mark: ✢ RWS — Se útil, deixe rastro.
```
