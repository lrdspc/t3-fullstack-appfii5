# CI/CD e Integração de Ambientes

Objetivo
- Garantir pipeline contínuo de qualidade, segurança e deploy para staging/produção, com variáveis de ambiente corretamente segregadas.

Arquitetura de Pipeline
- GitHub → Vercel (deploy automático por branch/tag).
- GitHub Actions (CI): lint, typecheck, test, build; relatórios (coverage, Lighthouse opcional em preview).
- Supabase: provisionamento de projeto, políticas RLS, buckets de storage, chaves (anon/service-role) e webhooks.

Branches e Ambientes
- main → Produção (Vercel Production).
- develop → Staging (Vercel Preview persistente).
- feature/* → Previews efêmeros (Vercel Preview por PR).

Checklist de Integração
1) GitHub
   - Repositório conectado ao Vercel (importar projeto).
   - Secrets no GitHub para execução de CI (se necessário, ex.: RESEND_API_KEY para testes).
   - Proteção de branches (review obrigatório, status checks).
2) Vercel
   - Projeto criado com auto-deploy por PR.
   - Variáveis de ambiente por ambiente:
     - NEXT_PUBLIC_SUPABASE_URL
     - NEXT_PUBLIC_SUPABASE_ANON_KEY
     - SUPABASE_SERVICE_ROLE_KEY (server-only)
     - RESEND_API_KEY (server-only)
     - VAPID_PUBLIC_KEY / VAPID_PRIVATE_KEY (push)
     - AUTH_SECRET (se NextAuth continuar em fase de transição)
   - Analytics habilitado; domínios e SSL configurados na fase LAUNCH.
3) Supabase
   - Projeto provisionado.
   - Tabelas do domínio criadas (users/invites/exercicios/treinos/treino_exercicios/execucoes/mensagens).
   - RLS:
     - aluno: acesso a registros onde aluno_id = auth.uid().
     - personal: acesso a registros vinculados a seus alunos (personal_id).
     - mensagens: remetente_id == auth.uid() OR destinatario_id == auth.uid().
   - Buckets de storage:
     - exercicios-media (privado, ACL via RLS).
   - Chaves:
     - anon: client-side.
     - service-role: server-side; nunca expor em client.
   - Email provider:
     - Resend/Postmark configurado (DKIM/SPF na LAUNCH).
4) Push Notifications
   - Geração de VAPID keys (web-push).
   - Registro SW e permissão (fase BUILD).
   - Tópicos: novo treino, mensagens, inatividade.

GitHub Actions (exemplo de jobs)
- jobs:
  - lint: run eslint/prettier check.
  - typecheck: tsc --noEmit.
  - test: vitest/jest (unit + integração leve).
  - build: next build.
  - lighthouse (opcional): rodar em preview e anexar artefatos.

Gate de Qualidade
- PR precisa:
  - Lint e typecheck OK.
  - Testes OK (mínimo cobertura em módulos críticos do convite/treino/chat).
  - Build OK.
  - Lighthouse ≥ 90 (mobile) para páginas principais (Home, Dashboard, Treino).

Segurança de Secrets
- Nunca commitar chaves reais; usar .env.example como referência.
- Em Vercel, usar “Environment Variables”; em GitHub Actions, “Repository Secrets”.
- Rotacionar chaves em incidentes; audit trail em Supabase.

Entregáveis (PLAN)
- Este documento (CI-CD.md).
- “Ops-Env.md” com todas as variáveis necessárias e escopos (client/server).
- Checklist pronto para execução ao iniciar BUILD.