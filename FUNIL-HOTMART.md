# Funil de vendas Hotmart — mapadelplacer.site

O widget de Funil de Vendas do Hotmart é **o mesmo snippet nas quatro etapas**.
Ele não sabe qual oferta mostrar pelo código: quem decide é a **URL cadastrada
no painel do Hotmart**. Por isso cada etapa precisa da sua própria rota, e a
rota precisa estar cadastrada lá antes de funcionar.

## Snippet (idêntico nas 4 etapas)

```html
<div id="hotmart-sales-funnel"></div>
<script src="https://checkout.hotmart.com/lib/hotmart-checkout-elements.js"></script>
<script>
checkoutElements.init('salesFunnel').mount('#hotmart-sales-funnel')
</script>
```

## Etapas

| Etapa      | Rota          | Arquivo          | Status                    |
|------------|---------------|------------------|---------------------------|
| Upsell 1   | `/upsell1`    | `upsell1.html`   | chat antigo, no ar        |
| Upsell 1 (novo) | `/up1`   | `up1.html`       | "Protocolo Reconexión en Pareja: El Plan Guiado de 14 Días"; página estilo VSL; widget `salesFunnel`; CTA no 7:02; falta player VTURB e publicar oferta no Hotmart |
| Downsell 1 | `/down1`  | `down1.html`     | mesmo produto do UP1, condição especial US$19,50 (só à vista); design adaptado do `up1.html`; widget `salesFunnel`; falta player VTURB e publicar oferta no Hotmart |
| Upsell 2   | `/upsell2` `/up2` | `upsell2.html` | copy Pompoarismo de las Diosas; widget `salesFunnel` no botão verde; falta cadastrar oferta no Hotmart |
| Downsell 2 | `/downsell2`  | —                | aguardando a estrutura    |

> Upsell 2 = **Pompoarismo de las Diosas** (US$47 à vista ou 5x de US$15,67, garantia 7 dias, âncora US$147).
> A oferta antiga "Cofre Secreto de la Pasión" saiu da rota `/upsell2` e ficou em `cofre-secreto/` (sem rewrite público).
> **Mecânica do checkout (up1.html e upsell2.html):** ao clicar no botão verde, monta-se o widget `salesFunnel` do Hotmart (`checkout.hotmart.com/lib/hotmart-checkout-elements.js` → `checkoutElements.init('salesFunnel').mount('#hotmart-sales-funnel')`) — checkout de 1 clique. O widget só pinta se a pessoa chegar com token de compra na URL, vindo do passo anterior do funil no painel Hotmart. Sem isso, cai num fallback ("Reintentar" / "Ir al checkout").

## Como adicionar uma etapa nova

1. Colocar o HTML da página na raiz como `<etapa>.html`.
2. Adicionar a rota no `vercel.json`, **antes** do catch-all — a última regra
   (`/(.*)` → `/index.html`) engole qualquer rota que venha depois dela:

   ```json
   { "source": "/downsell1", "destination": "/downsell1.html" }
   ```
3. Integrar o widget do mesmo jeito que em `upsell1.html`: o container
   `#hotmart-sales-funnel` é criado junto com o passo da oferta, e o
   `mount()` só roda depois que ele existe no DOM.
4. Cadastrar a URL completa da etapa no painel do Hotmart.

## Detalhes da integração no upsell1

- A lib é pedida logo na abertura da página, não no passo da oferta, para o
  widget aparecer instantâneo na hora da decisão.
- O `mount()` acontece quando o container é criado pelo chat, não no load —
  montar antes falharia, porque a `<div>` ainda não existe.
- Se o widget não pintar nada em 7 segundos (bloqueado, sem token de compra na
  URL, erro do Hotmart), aparecem os botões de "Reintentar" e "Continuar a mi
  acceso", para o chat nunca ficar sem saída.
- A copy dos botões de comprar e recusar vem do Hotmart, não deste arquivo.
  Inclusive o idioma: se saírem em português numa página em espanhol, o ajuste
  é no painel do Hotmart.
