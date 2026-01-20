# Teste Front-end (Vue) — Pesquisa de Satisfação

## Objetivo
Construir um mini sistema em **Vue 3** onde um usuário consegue **responder uma pesquisa** (em formato step-by-step, estilo widget) e depois existe uma área interna que **lista, edita e exclui respostas**.

O foco é avaliar: organização do código, componentização, lógica de negócio, manipulação de dados, UX de formulários, e uso correto do browser storage.

Você pode usar IA (ChatGPT/Copilot/etc). 
Declare no README como usou (ferramenta + objetivo) e esteja preparado para explicar o código e as decisões. O que será avaliado é entendimento, qualidade e consistência (não apenas ter "algo funcionando").

Na pasta **assets**, há imagens como referência visual. Elas foram propositalmente mantidas sem muitos detalhes para não enviesar sua solução de layout. Use-as apenas para entender o que cada parte do teste representa.

---

## Stack obrigatória
- **Vue 3**
- **Vite**
- **TypeScript**
- **Tailwind CSS**
- **ESLint + Prettier**

> Não usar backend. Persistência deve ser via **localStorage**.

---

## Funcionalidades obrigatórias

### 1) Responder Pesquisa (fluxo step-by-step)
**Rota sugerida:** `/p/:id`

Implemente a experiência de responder uma pesquisa em cards, com navegação **Previous / Next**, inspirada em widgets de survey.

A pesquisa deve conter **4 passos** (fixos) na ordem abaixo:

1. **Mood (escolha única por ícone)**
   - Pergunta: “Qual é o seu humor hoje?”
   - Opções: 5 ícones (ex.: 😀 🙂 😐 🙁 😡)
   - Obrigatório para avançar

2. **CSAT (nota 1–5)**
   - Pergunta: "Que nota você daria para esta página?”
   - Escala 1–5 (botões)
   - Obrigatório para avançar

3. **Newsletter (email opcional)**
   - Pergunta: “Você gostaria de se inscrever em nossa lista de e-mails?”
   - Input email com validação (apenas se preenchido)
   - Opcional

4. **Feature choice + comentário (opcional)**
   - Pergunta: “E se... você pudesse escolher o próximo recurso que desenvolveríamos? Qual deveria ser?”
   - Opções: Feature A / Feature B / Feature C (escolha única)
   - Campo texto opcional: “Digite sua resposta”

**Ao finalizar e enviar:**
- Salvar a resposta no storage
- Exibir tela/estado “Obrigado” com mensagem de sucesso

**Regras de UX:**
- “Próxima/Next” deve ficar desabilitado enquanto a pergunta obrigatória não for respondida
- Exibir erro de validação quando tentar avançar sem preencher obrigatório
- Mostrar loading no botão “Enviar/Submit” (simulado com timeout curto, ex. 300ms)

---

### 2) Identificação de “Respondente” (regra de negócio)

Na tela de listagem, as respostas devem ser **agrupadas por respondente**. Ou seja, o sistema precisa conseguir identificar quando uma nova resposta pertence à **mesma pessoa** ou a **uma pessoa diferente**.

Você deve definir um critério para gerar um `respondentId` e **explicar no README** como ele funciona.

#### Requisitos do critério
- **Determinístico:** a mesma pessoa/dispositivo deve gerar o mesmo `respondentId` em acessos futuros (sempre que possível).
- **Coerente com a realidade:** a regra deve fazer sentido em um cenário real de pesquisa (ex.: alguém que responde mais de uma vez).
- **Reprodutível:** deve ser implementável e previsível, evitando “IDs aleatórios” a cada envio.

#### Exemplos de abordagens aceitas (apenas sugestões)
- Se o usuário informar email, usar o email (normalizado) como `respondentId`; caso contrário, usar um `deviceId` persistido no `localStorage`.
- Gerar um `respondentId` na primeira visita e persistir (simulando identificação por dispositivo/sessão).
- Criar um hash simples baseado em características do navegador (ex.: timezone + language + userAgent) e persistir o resultado.

> Essas sugestões **não são obrigatórias**. Queremos ver sua proposta e o raciocínio por trás dela. O importante é que sua solução permita reconhecer quando alguém está respondendo novamente e agrupar corretamente as respostas na listagem.

---

### 3) Área interna — Listar Respostas (com agrupamento)
**Rota sugerida:** `/responses`

Criar uma tela de listagem com:
- Agrupamento por **respondente** (ex.: “Respondent R-10293” ou email)
- Dentro de cada grupo, listar as respostas desse respondente (ao menos mostrar data e resumo)
- Ações por resposta:
  - **Editar**
  - **Excluir**
  - (Opcional) Ver detalhe

**Estados obrigatórios:**
- empty state (sem respostas)
- confirmação de exclusão (modal)
- feedback visual (toast ou mensagem de sucesso/erro)

---

### 4) Editar Resposta
**Rota sugerida:** `/responses/:id/edit` (ou modal)

- Carregar os dados da resposta
- Permitir alterar os campos (mood, csat, email, feature, comentário)
- Validar campos obrigatórios (mood e csat)
- Salvar atualizações no storage
- Mostrar feedback de sucesso

---

### 5) Excluir Resposta
- Excluir uma resposta individual
- Pedir confirmação antes
- Atualizar a listagem após exclusão

---

## Persistência (obrigatório)
Use `localStorage` para armazenar:
- lista de respostas
- identificação do respondente (conforme sua regra)

---

## Requisitos de qualidade

### Arquitetura (obrigatório)
- Separar responsabilidades:
  - `src/services/storage.ts` (leitura/escrita no localStorage)
  - `src/services/respondent.ts` (regra do respondentId)
  - `src/pages/*` ou `src/views/*`
  - `src/components/*` (componentes reutilizáveis)
- Evitar lógica duplicada
- Nomes claros para variáveis, funções e componentes

### Testes (diferencial, não obrigatório)
- Testar a função de agrupamento por respondente
- Testar validação do fluxo (mood/csat)

---

## Rotas sugeridas (Não obrigatório)
- `/` — Start (botão “Start Survey” e link para “View Responses”)
- `/p/:id` — Survey Flow
- `/responses` — Listagem
- `/responses/:id/edit` — Edição

> O `:id` da pesquisa pode ser fixo (ex.: `default`) já que a pesquisa é sempre a mesma.

---

## Entrega
1. Repositório Git (GitHub/GitLab)
2. README contendo:
   - como rodar o projeto (`npm i`, `npm run dev`, etc.)
   - descrição rápida da arquitetura/pastas
   - **regra escolhida para identificar respondente**
3. Deploy (Vercel/Netlify/GitHub Pages) com link no README

### Scripts obrigatórios
- `dev`
- `build`
- `lint`

---

## O que será avaliado
- Vue: componentização, organização das páginas, estado e reatividade
- Lógica: navegação do stepper, validações, agrupamento por respondente
- Persistência: leitura/escrita consistente no storage
- UX: empty/loading/error states, feedback, modais
- Qualidade: TypeScript, ESLint/Prettier, legibilidade, commits
