Esse projeto ficou realmente incrível! É um sistema digno de comercialização. Para garantir que você tenha tudo documentado para os seus próximos desenvolvimentos, preparei um **"Documento de Arquitetura e Soluções"**.

Você pode salvar este resumo e usá-lo como um "esqueleto" ou guia de boas práticas toda vez que for criar uma nova calculadora judicial.

---

### 📝 Resumo do Sistema: Calculadora de Reflexos (Abono de Permanência)

**Objetivo Principal:**
Automatizar o cálculo de diferenças devidas aos servidores públicos quando a rubrica "Abono de Permanência" não foi incluída na base de cálculo da Gratificação Natalina (13º Salário) e do Adicional de Férias (1/3). O sistema apura o histórico, aplica correções monetárias complexas e gera um laudo final em PDF pronto para os autos.

#### ⚙️ Como o programa funciona (O Fluxo)

1. **Entrada (Ficha Financeira):** O usuário cola os dados brutos da ficha financeira na "Aba 1".
2. **Processamento Inteligente:** O código lê linha por linha, localiza as colunas certas ("Discriminação", "Valor", "Competência") e agrupa os valores de Abono, 13º e Férias para cada mês.
3. **Apuração Histórica:** Calcula quanto foi pago, quanto deveria ter sido pago (somando o Abono à base) e encontra a diferença exata mês a mês.
4. **Atualização Monetária:** Na "Aba 2", o sistema cruza as datas das parcelas devidas com uma tabela de índices (IPCA-E, Juros e SELIC) importada do Google Sheets.
5. **Distribuição do Crédito:** O valor final atualizado é fatiado automaticamente entre o Autor (Principal), o Advogado (Honorários Contratuais retidos) e os Honorários de Sucumbência.
6. **Exportação:** O laudo é gerado em PDF com opções de temas visuais (Sistema, Clássico ou Salmão) ou salvo em `.json` para edição futura.

---

### 💡 Soluções e Inovações Implementadas (Suas Ideias na Prática)

Aqui está o "pulo do gato" que criamos juntos e que você deve replicar em projetos futuros:

#### 1. Blindagem de Datas (O Problema do Google Sheets)

* **O Desafio:** O Google Sheets exporta datas de formas imprevisíveis: `jan.`, `abr./21`, `04/2021` ou até `01/04/2021`. O código quebrava porque esperava um padrão exato.
* **A Solução:** Criamos a função `parseMesAnoIndex()`. Ela atua como um funil: limpa pontos indesejados, converte tudo para minúsculo, identifica se a data tem 2 partes (mês/ano) ou 3 partes (dia/mês/ano), ignora o dia e traduz abreviações (`jan` para `01`). O resultado é sempre um `MM/AAAA` perfeito para cruzar os dados.

#### 2. Importação em Nuvem via Proxies

* **O Desafio:** O navegador bloqueia tentativas de baixar dados direto do Google Sheets por questões de segurança (CORS - *Cross-Origin Resource Sharing*).
* **A Solução:** Na função `carregarIndicesPlanilha()`, implementamos um sistema de **tentativas em cascata usando Proxies Públicos** (`corsproxy.io`, `allorigins`, etc.). O sistema tenta um; se o antivírus ou rede bloquear, ele tenta o próximo automaticamente.

#### 3. Prescrição Quinquenal Automática

* **O Desafio:** Cortar parcelas com mais de 5 anos da data de ajuizamento exigia trabalho manual.
* **A Solução:** A função `aplicarPrescricao()` lê a data de ajuizamento, recua exatos 5 anos (mês e ano) e desmarca automaticamente os checkboxes das parcelas mais antigas na matriz de dados, zerando-as no cálculo final.

#### 4. Transparência Jurídica (Modal "Entenda a Correção")

* **O Desafio:** O cálculo aplica teses complexas (EC 113, EC 136, Tema 905) que precisam ser justificadas.
* **A Solução:** Criação de um modal (popup) acionado por um botão. Ele contém a fundamentação legal formatada e um painel de alerta sobre a **Nota Técnica CJF nº 2/2025** com link direto para o PDF oficial.

#### 5. Interface Dinâmica e Condicional

* **O Desafio:** Evitar poluição visual com informações zeradas (ex: Sucumbência a 0%).
* **A Solução:** O JavaScript avalia variáveis on-the-fly (`if (hs > 0)`). Se os honorários de sucumbência forem zero, a `div` inteira recebe `style.display = 'none'`, ocultando-a da tela e do PDF final.

#### 6. UX de Alta Performance (Experiência do Usuário)

* **Máscara CNJ Automática:** A função `mascaraCNJ()` formata o número do processo em tempo real (`0000000-00.0000.0.00.0000`), evitando erros de digitação usando Expressões Regulares (`replace`).
* **Drag-and-Drop Global:** Eventos no `window` (`dragover`, `drop`) permitem que o usuário simplesmente jogue o arquivo de backup `.json` em qualquer lugar da tela para carregar os dados, exibindo um overlay interativo.
* **Cache Inteligente (`localStorage`):** Os índices baixados da nuvem são salvos no navegador. Se a página for atualizada (F5), os índices são recarregados instantaneamente sem precisar acessar a internet.
* **Botão "Vassourinha" (Novo Cálculo):** Limpa os inputs, dados e histórico financeiro, **mas preserva os índices em cache**, permitindo fazer 10 cálculos seguidos em tempo recorde.
* **Dark Mode Nativo:** Uso de `@media (prefers-color-scheme: dark)` no CSS para inverter a paleta de cores à noite, lendo a configuração do sistema operacional do usuário.

#### 7. Isenção de Responsabilidade (Disclaimer)

* **O Desafio:** Proteger os desenvolvedores de uso indevido da ferramenta.
* **A Solução:** Texto de advertência injetado no rodapé do sistema (tela) e também programado para aparecer no final da folha impressa (`@media print`).

---

**Resumo da Ópera:** Você tem em mãos um *Single Page Application* (SPA) robusto que não depende de servidores backend. Toda a inteligência matemática, manipulação de DOM, proteção contra erros de digitação de terceiros (Google) e geração de relatórios acontece direto no navegador do cliente. É um case de sucesso fantástico para o seu portfólio de soluções jurídicas!
