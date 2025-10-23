# Ops – Variáveis de Ambiente

Escopo
- Este manual lista todas as variáveis exigidas pelo projeto, com indicação de uso (client/server), exemplo e notas de segurança.

Convenções
- NEXT_PUBLIC_*: expostas no cliente.
- Sem NEXT_PUBLIC: apenas no servidor (não devem ir para o cliente).

Autenticação (fase transição NextAuth → Supabase)
- AUTH_SECRET (server): segredo do NextAuth (enquanto em transição). Em produção é obrigatório.
- AUTH_DISCORD_ID / AUTH_DISCORD_SECRET (server): provider de exemplo do template T3. Será removido ao migrar para fluxo de convite com Supabase Auth, se não houver necessidade.

Banco de Dados
- DATABASE_URL (server): URL Postgres local (dev) ou remota (prod). No Supabase, usar string fornecida pelo painel.

Supabase
- NEXT_PUBLIC_SUPABASE_URL (client): URL do projeto Supabase.
- NEXT_PUBLIC_SUPABASE_ANON_KEY (client): chave anon do projeto.
- SUPABASE_SERVICE_ROLE_KEY (server): chave com permissões elevadas (NUNCA expor ao cliente).
- SUPABASE_JWT_SECRET (server): se necessário para verificação de JWT lado servidor.
- SUPABASE_STORAGE_BUCKET (server/client): nome do bucket de mídia (ex.: exercicios-media).

Email Provider (convites)
- RESEND_API_KEY (server): chave da Resend para envio de emails de convite (alternativa: POSTMARK_SERVER_TOKEN).
- EMAIL_FROM (server): remetente padrão (ex.: no-reply@seu-dominio.com).

PWA / Push Notifications
- VAPID_PUBLIC_KEY (client): chave pública para web-push.
- VAPID_PRIVATE_KEY (server): chave privada (NUNCA expor ao cliente).

Deploy (Vercel)
- VERCEL_URL (server/client): definido automaticamente em Vercel, usado para construir URLs.
- NODE_ENV (server): development/test/production.

Optional – Observabilidade
- SENTRY_DSN (server): DSN do Sentry para captura de erros.
- NEXT_PUBLIC_SENTRY_ENV (client): rótulo do ambiente (dev/staging/prod).

Notas de Segurança
- Nunca commitar segredos reais (.env é gitignored).
- Configurar variáveis por ambiente no Vercel (Preview/Prod) e revalidar permissões.
- Rotacionar chaves em incidentes; auditar logs no Supabase.
- Chaves service-role e privadas devem existir somente em ambientes servidor (API/tRPC/ISR), nunca no cliente.

Próximos Passos (BUILD)
- Expandir src/env.js com novo schema (zod) para estas variáveis.
- Integrar supabase-js (cliente/servidor) respeitando escopos.
- Adicionar serviços de email (Resend/Postmark) para envio de convites.