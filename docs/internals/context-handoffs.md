# Context handoffs

Portable provider and fork handoffs transfer a budgeted selection of intact user and assistant
messages. They do not reconstruct native reasoning, tool-call state, approvals, or attachments.
The current user input stays separate and is never shortened to make history fit.

[`ContextHandoffServiceV2`](../../apps/server/src/orchestration-v2/ContextHandoffService.ts)
selects eligible history for provider switches, legacy continuation, and fork merge-back.
Provider handoffs cover either full eligible app history or the delta since the target provider
last participated. Fork merge-back selects the child's eligible delta. Coverage and omitted-item
references let the agent retrieve saved history that does not fit.

The [shared budget](../../packages/provider-core/src/server/handoffBudget.ts) accounts for the
target model, existing context, current input, attachments, and reserved capacity. See
[portable handoff limits](../user/portable-handoffs.md#context-limits) for configuration and
fallback estimates. Codex can receive native historical messages when its installed version
supports them; other adapters render the same selection as attributed context.

The service also retains diagnostic summaries: fork summaries compact eligible activity to
240-character excerpts, and the legacy migration summary keeps a transcript suffix within
32,000 characters. Those summaries are separate from the intact message selection used for
continuation. Neither limit describes the portable history budget.
