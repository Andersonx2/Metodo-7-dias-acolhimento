# Método 7 Dias de Acolhimento — página de vendas e plano dos R$10k

Página de vendas pronta, construída a partir do material do método (guia de atendimento + 6 bônus) e da oferta já cadastrada na Lastlink.

Checkout usado em todos os CTAs: `https://lastlink.com/p/CF4E6DF14/checkout-payment/`

---

## 0. Estado atual (publicado em 15/10/2026)

| Item | Estado |
| :-- | :-- |
| Página no ar | https://metodo-7-dias-acolhimento.vercel.app |
| Repositório | github.com/Andersonx2/Metodo-7-dias-acolhimento, branch `main` |
| Deploy | Vercel, projeto `metodo-7-dias-acolhimento`, produção automática a cada push na `main` |
| Container GTM | GTM-TKW5TWQT, conta FreedomPulse, versão 2 publicada |
| Pixel Meta | 1830931954135863 (dataset ebookFreedom), disparado pelo GTM |
| Checkout | https://lastlink.com/p/CF4E6DF14/checkout-payment/ |
| Order bump | Ativo na oferta do Método: ebook por R$37, produto `Atendimento Estrategico para Psicologas e Psicologos` |
| Venda liberada | Não. Depende do envio de documentos na Lastlink e da carteira vinculada nos dois produtos. |

Para atualizar a página, edite o `index.html` e faça push na `main`. O Vercel republica sozinho.

---

## 1. Bloqueios que impedem a venda hoje

| Bloqueio | Onde resolver | Impacto |
| :-- | :-- | :-- |
| Identidade não verificada na Lastlink | Produto → aviso amarelo → "Enviar documentos" | O checkout não libera venda. Nada roda antes disso. |
| Conta de recebimento | Produto → Carteiras | Sem carteira configurada o dinheiro não cai. |
| Página sem prova social | Seção de depoimentos comentada no HTML | Converte menos no tráfego frio. |
| Order bump não existe | Produto → Ofertas → Order Bump | Sem ele o ticket médio fica travado em R$147. |

---

## 2. Subir no GitHub e publicar

O repositório `Andersonx2/Metodo-7-dias-acolhimento` está vazio. Esta sessão não tem credencial de escrita na sua conta, então o push é seu.

```bash
git clone https://github.com/Andersonx2/Metodo-7-dias-acolhimento.git
cd Metodo-7-dias-acolhimento
# copie index.html para esta pasta
git add index.html
git commit -m "feat: pagina de vendas do Metodo 7 Dias de Acolhimento"
git push origin main
```

Publicação pelo Vercel:

```bash
npm i -g vercel
vercel --prod
```

Alternativa sem build: GitHub Pages em Settings → Pages → Branch `main` → `/root`.

Depois aponte o domínio próprio. Página de vendas em domínio próprio converte melhor e permite rastreamento completo.

---

## 3. Tracking

O bloco de configuração fica no topo do `index.html` e já está preenchido:

```js
window.M7D = {
  CHECKOUT_URL: "https://lastlink.com/p/CF4E6DF14/checkout-payment/",
  GTM_ID: "GTM-TKW5TWQT",
  PIXEL_ID: "1830931954135863",
  DEADLINE: "2026-10-19T23:59:59-03:00",
  PRECO: 147.00
};
```

O que está configurado no GTM, container GTM-TKW5TWQT, versão 4 publicada:

| Item | Tipo | Dispara em |
| :-- | :-- | :-- |
| Meta - Pixel base e ViewContent | HTML personalizado | All Pages |
| Meta - InitiateCheckout | HTML personalizado | Evento `inicio_checkout` |
| Meta - ScrollProfundo75 | HTML personalizado | Evento `scroll_pagina` com profundidade igual a 75 |
| Inicio de checkout | Acionador | Evento personalizado `inicio_checkout` |
| Scroll 75 por cento | Acionador | Evento personalizado `scroll_pagina` |
| dlv - profundidade | Variável | Chave `profundidade` da camada de dados |

Toda a camada do Meta vive no GTM. O `index.html` não carrega `fbevents.js` e não dispara nenhum evento de pixel, ele apenas alimenta a camada de dados. Para trocar de pixel, mude a tag no GTM e publique, sem tocar no código.

Consequência a considerar: quem usa bloqueador que derruba o googletagmanager.com perde o pixel inteiro, já que ele passa a depender do container. Esse é o custo de centralizar tudo no GTM.

O evento ScrollProfundo75 serve para montar público de remarketing de quem leu até o fim da página.

### Eventos já implementados no dataLayer

| Evento | Quando dispara | Parâmetros |
| :-- | :-- | :-- |
| `ver_pagina_vendas` | Carga da página | produto, valor, moeda |
| `clique_cta` | Clique em CTA que rola a página | cta_origem |
| `inicio_checkout` | Clique em CTA que vai para a Lastlink | cta_origem, valor, moeda |
| `scroll_pagina` | 25%, 50%, 75% e 90% da página | profundidade |
| `tempo_na_pagina` | 30s, 60s e 120s | segundos |

No GTM, crie uma tag de evento personalizado do Meta para `inicio_checkout` com valor 147 e moeda BRL. O Pixel nativo já dispara `ViewContent` e `InitiateCheckout` quando o `PIXEL_ID` está preenchido.

### UTMs

Os parâmetros `utm_source`, `utm_medium`, `utm_campaign`, `utm_content`, `utm_term`, `fbclid`, `gclid`, `src` e `sck` são capturados da URL da página e repassados para o link do checkout automaticamente. Também vai junto um parâmetro `cta` indicando qual botão foi clicado, o que mostra quais seções vendem.

Parâmetro de URL para usar nos anúncios do Meta:

```
utm_source=meta&utm_medium=cpc&utm_campaign={{campaign.name}}&utm_content={{ad.name}}&utm_term={{adset.name}}
```

### Evento de compra

O `Purchase` precisa vir da Lastlink, porque a compra acontece no domínio dela. Vá em Produto → Integrações → Facebook Ads e conecte o mesmo pixel. Confirme no Gerenciador de Eventos que a compra aparece antes de escalar a verba.

---

## 4. Order bump

Você já tem o ativo pronto para o bump: o ebook **Atendimento Estratégico para Psicólogas(os)** que está no seu Drive. Ele complementa o método sem canibalizar a oferta principal.

Já configurado:

- Produto: `Atendimento Estrategico para Psicologas e Psicologos`, com o PDF de 17 páginas anexado
- Oferta: `Scripts Completos de Atendimento`, pagamento único de R$ 37,00
- Order bump na oferta do Método: título "Leve junto o guia completo de atendimento", CTA "Quero os scripts tambem", status ativo

Falta vincular a carteira de recebimento no produto do bump, o que só você pode fazer.

Com 30% de adesão, o ticket médio sobe de R$147 para cerca de R$158. São 5 vendas a menos para chegar nos R$10k.

---

## 5. A matemática dos R$10k

| Cenário | Ticket médio | Vendas necessárias | Conversão da página | Cliques necessários | Verba estimada (CPC R$1,20) |
| :-- | --: | --: | --: | --: | --: |
| Sem bump | R$ 147 | 68 | 1,0% | 6.800 | R$ 8.160 |
| Sem bump | R$ 147 | 68 | 2,0% | 3.400 | R$ 4.080 |
| Com bump 30% | R$ 158 | 64 | 2,0% | 3.200 | R$ 3.840 |
| Com bump 30% | R$ 158 | 64 | 3,0% | 2.133 | R$ 2.560 |

Leitura honesta desses números: tráfego frio direto para checkout de R$147 costuma rodar com CPA entre R$40 e R$80 no Brasil. Para 64 vendas em sete dias, a verba realista fica entre R$3.000 e R$4.500, com faturamento bruto de R$10k e margem antes das taxas da plataforma entre R$5.500 e R$7.000.

Se a verba disponível for menor que R$3.000, o caminho de maior probabilidade é captar lead com material gratuito e vender por WhatsApp e e-mail, porque o custo por venda cai bastante e o prazo se estende para duas ou três semanas.

---

## 6. Estrutura de campanha no Meta

**Campanha 1 · Vendas (conversão: Compra)**

- Orçamento na campanha (CBO), começando em R$250 por dia
- Conjunto A: público aberto, 25 a 55 anos, Brasil, sem segmentação detalhada
- Conjunto B: interesses em Psicologia, Psicanálise, Terapia cognitivo-comportamental, CRP, Consultório
- Conjunto C: lookalike de quem visitou a página, criado depois de 3 dias de dados
- 3 criativos por conjunto, rodízio semanal

**Campanha 2 · Remarketing**

- Público: visitou a página nos últimos 7 dias e não comprou
- Orçamento: R$60 por dia
- Criativo focado em garantia e nos bônus

Enquanto o pixel não tiver volume de compra, otimize por `InitiateCheckout` e troque para Compra assim que passar de 15 eventos na semana.

---

## 7. Cinco copies para o Gerenciador

Atenção à política de atributos pessoais do Meta: evite afirmar a profissão de quem lê ("você, psicóloga"). As copies abaixo já usam construção impessoal.

### Copy 1 · A conversa que trava

**Texto principal**
A pessoa manda mensagem, conta o que está sentindo, pergunta o valor e some. Isso acontece em quase todo consultório que recebe contato pelo WhatsApp.
A janela de decisão de quem procura terapia dura cerca de uma semana. O Método 7 Dias de Acolhimento organiza oito mensagens dentro dessa janela, com texto pronto para cada momento, triagem estruturada e linguagem alinhada ao Código de Ética.
Guia completo mais seis materiais de aplicação por R$147, com garantia de 7 dias.

**Título:** Do primeiro "oi" até a sessão marcada em 7 dias
**Descrição:** Cadência pronta de 8 mensagens para atendimento clínico no WhatsApp

### Copy 2 · O preço na hora errada

**Texto principal**
O valor costuma entrar na conversa antes de a pessoa entender o que vai receber por ele. A partir daí a resposta demora, esfria e some.
O método inverte a ordem. Primeiro vem o acolhimento, depois a triagem de 20 minutos, depois o plano em três linhas. O valor aparece no terceiro dia, como consequência do que já foi combinado.
Acesso imediato, R$147, com os textos prontos para copiar.

**Título:** O valor entra no dia 3, depois do plano
**Descrição:** Guia + 6 materiais prontos para aplicar ainda esta semana

### Copy 3 · A dúvida ética

**Texto principal**
Organizar o atendimento inicial cabe dentro do Código de Ética. O que não cabe é depoimento identificável de paciente, promessa de cura e linguagem sensacionalista.
O Método 7 Dias de Acolhimento vem com um checklist de divulgação ética que mostra exatamente onde está cada limite, além da cadência completa de mensagens, da ficha de triagem e do termo de prestação de serviços.
R$147 com garantia incondicional de 7 dias.

**Título:** Atendimento organizado dentro das regras da profissão
**Descrição:** Checklist de divulgação ética incluso no método

### Copy 4 · A agenda com buracos

**Texto principal**
Horário recusado para uma pessoa, reservado para outra, e a outra falta. O prejuízo de uma falta é maior do que o valor da sessão.
O Pacote Anti-falta que acompanha o método traz os lembretes, o intervalo de envio e a política de reagendamento que seguram a primeira sessão. Vem junto com a cadência de 8 mensagens, a planilha de funil e mais quatro materiais.
Acesso imediato por R$147.

**Título:** Menos faltas na primeira sessão
**Descrição:** Lembretes, política de reagendamento e cadência pronta

### Copy 5 · Números do funil

**Texto principal**
Oitenta por cento de resposta no primeiro contato. Sessenta por cento de conversão da triagem para proposta. Cinquenta por cento da proposta para sessão paga. Dez por cento de falta.
Essas são as metas de referência do Método 7 Dias de Acolhimento, acompanhadas pela planilha de funil que vem junto com o guia.
Tudo por R$147, com garantia de 7 dias.

**Título:** As metas de um funil clínico organizado
**Descrição:** Planilha de funil com as fórmulas prontas

---

## 8. Checklist antes de ligar o tráfego

1. Documentos enviados e identidade aprovada na Lastlink
2. Carteira de recebimento configurada
3. Order bump de R$37 criado e testado
4. Pixel conectado também na Lastlink, em Integrações, para o evento de Compra
5. Domínio próprio apontado no Vercel, em Settings e Domains
6. Conferir o carregamento do GTM em aba anônima, sem extensões de bloqueio
7. Uma compra de teste feita com cupom de 100%, para confirmar o evento de Compra no Gerenciador de Eventos
8. Prazo real definido para a condição de lançamento, no campo `DEADLINE`
9. Seção de depoimentos preenchida com depoimentos reais de quem comprou, nunca de pacientes
10. Verba diária definida e limite de gasto da conta conferido

---

## 9. Pontos da página que pedem sua revisão

- **Conteúdo dos 7 dias.** A cadência foi escrita a partir do material de atendimento do método. Confira cada dia contra o PDF do guia antes de rodar tráfego, para que a página prometa exatamente o que o guia entrega.
- **Depoimentos.** A seção existe no HTML em comentário. Publique apenas com depoimentos reais e autorizados.
- **Prazo.** A contagem regressiva aparece quando `DEADLINE` está preenchido. Use uma data que você vai cumprir de fato.
- **Autoria.** A página não afirma credenciais clínicas. Se quiser assinar com um nome e um CRP, me avise que eu incluo a seção.
