# Matriz de Papéis e Acessos

Visão
- Garantir que cada ação no sistema respeite papel (personal|aluno) e vínculo (aluno→personal), com políticas RLS.

Recursos e Ações

Usuários
- Personal:
  - Ler/editar seu perfil.
  - Ler perfis de seus alunos (nome/foto/email).
- Aluno:
  - Ler/editar seu perfil.
  - Não pode ler perfil de outros alunos; não pode editar personal.

Convites
- Personal:
  - Criar/editar/revogar convites para seus futuros alunos.
  - Listar convites emitidos por si.
- Aluno:
  - Não pode criar convites; apenas aceitar quando receber o link.

Biblioteca de Exercícios
- Personal:
  - Criar/editar exercícios autorais (autor_personal_id = seu id).
  - Ler biblioteca global e autoral.
- Aluno:
  - Ler biblioteca global e do seu personal.
  - Não pode criar/editar exercícios.

Treinos
- Personal:
  - Criar/editar treinos para seus alunos (aluno_id → personal_id = seu id).
  - Listar/visualizar treinos e observações.
- Aluno:
  - Ler seus treinos (aluno_id = seu id).
  - Não pode criar/editar treinos.

Execuções
- Personal:
  - Ler execuções dos seus alunos; ver RPE/feedbacks.
- Aluno:
  - Criar execuções e feedbacks para seus treinos.
  - Editar apenas execuções próprias (ex.: correção de feedback do dia).

Mensagens (Chat)
- Personal:
  - Enviar e receber mensagens com seus alunos.
  - Ler apenas mensagens onde é remetente ou destinatário.
- Aluno:
  - Enviar e receber mensagens com seu personal.
  - Ler apenas mensagens onde é remetente ou destinatário.

Notificações
- Personal:
  - Disparar notificações (novo treino, inatividade, mensagem).
- Aluno:
  - Receber notificações.
  - Ajustar preferências (silenciar/permitir).

Regras RLS (Supabase) – Diretrizes
- users:
  - aluno: update select própria linha (id = auth.uid()).
  - personal: update select própria linha; select alunos onde personal_id = auth.uid().
- invites:
  - personal: full access nas linhas onde personal_id = auth.uid().
  - aluno: sem acesso direto (aceite via endpoint controlado).
- exercicios:
  - select: todos autorais do seu personal + globais.
  - insert/update: apenas quando autor_personal_id = auth.uid() (personal).
- treinos/treino_exercicios/execucoes:
  - aluno: acesso apenas às linhas onde aluno_id = auth.uid() (e joins coerentes).
  - personal: acesso às linhas onde aluno.personal_id = auth.uid().
- mensagens:
  - select: remetente_id = auth.uid() OR destinatario_id = auth.uid().
  - insert: remetente_id = auth.uid().
  - update lida: somente destinatário.

Checklist de Conformidade (TEST)
- 100% das rotas respeitam papel e vínculo.
- RLS efetiva (consulta direta ao banco sem passar pelo backend deve falhar fora da política).
- Uploads e leitura de mídia obedecem ACL definida.