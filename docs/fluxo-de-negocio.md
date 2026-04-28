# Fluxo de Negócio

## 1. Criar Agendamento

```
Cliente → POST /agendamentos
           │
           ▼
    AgendamentoController.salvarAgendamento()
           │
           ▼
    AgendamentoService.salvarAgendamento()
           │
           ├─ Calcula janela: [dataHoraAgendamento, dataHoraAgendamento + 1min]
           │
           ├─ Consulta: existe agendamento para o mesmo serviço nessa janela?
           │      ├─ SIM → lança RuntimeException("Horário já está preenchido")
           │      └─ NÃO → persiste o agendamento
           │
           ▼
    Agendamento salvo retornado (202 Accepted)
```

## 2. Listar Agendamentos do Dia

```
Cliente → GET /agendamentos?data=2026-05-10
           │
           ▼
    AgendamentoController.buscarAgendamentosDia()
           │
           ▼
    AgendamentoService.buscarAgendamentosDia()
           │
           ├─ Converte LocalDate → [00:00:00, 23:59:59]
           │
           └─ Repository: findByDataHoraAgendamentoBetween()
                  │
                  ▼
           Lista de Agendamento retornada (200 OK)
```

## 3. Atualizar Agendamento

```
Cliente → PUT /agendamentos?cliente=X&dataHoraAgendamento=Y
           │  Body: novos dados
           ▼
    AgendamentoController.alterarAgendamentos()
           │
           ▼
    AgendamentoService.alterarAgendamento()
           │
           ├─ Busca registro existente: findByDataHoraAgendamentoAndCliente(Y, X)
           │      ├─ NÃO encontrado → lança RuntimeException("Horário não está preenchido")
           │      └─ Encontrado → preserva o ID original, salva novos dados
           │
           ▼
    Agendamento atualizado retornado (202 Accepted)
```

## 4. Excluir Agendamento

```
Cliente → DELETE /agendamentos?cliente=X&dataHoraAgendamento=Y
           │
           ▼
    AgendamentoController.deletarAgendamento()
           │
           ▼
    AgendamentoService.deletarAgendamento()
           │
           └─ Repository: deleteByDataHoraAgendamentoAndCliente(Y, X)
                  │  (operação @Transactional)
                  ▼
           204 No Content
```

## Regras de Negócio

| Regra | Descrição |
|-------|-----------|
| **Conflito de horário** | Ao criar um agendamento, o sistema verifica se já existe outro agendamento para o **mesmo serviço** dentro de uma janela de **1 minuto** a partir do horário solicitado. Se existir, a operação é rejeitada. |
| **Identificação por cliente+horário** | As operações de atualização e exclusão identificam o registro pela combinação de **nome do cliente** e **data/hora exata** do agendamento. |
| **Preservação de ID na atualização** | Ao atualizar, o ID do registro original é mantido; apenas os campos do body são substituídos. |
| **Listagem por dia completo** | A busca por data retorna todos os agendamentos do dia, do início (00:00:00) ao fim (23:59:59). |
