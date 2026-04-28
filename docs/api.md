# Documentação da API

Base URL: `http://localhost:8080`

Todos os endpoints utilizam o prefixo `/agendamentos`.  
O formato de data/hora segue ISO 8601: `yyyy-MM-ddTHH:mm:ss`.

---

## POST /agendamentos

Cria um novo agendamento. O serviço verifica se já existe outro agendamento para o mesmo serviço dentro de uma janela de 1 minuto; caso exista, retorna erro.

**Status de retorno:** `202 Accepted`

### Body (JSON)

```json
{
  "servico": "Corte de cabelo",
  "profissional": "João",
  "dataHoraAgendamento": "2026-05-10T10:00:00",
  "cliente": "Maria",
  "telefoneCliente": "11999999999"
}
```

### Resposta de sucesso

```json
{
  "id": 1,
  "servico": "Corte de cabelo",
  "profissional": "João",
  "dataHoraAgendamento": "2026-05-10T10:00:00",
  "cliente": "Maria",
  "telefoneCliente": "11999999999",
  "dataInsercao": "2026-04-28T09:00:00"
}
```

### Erro de conflito

```
RuntimeException: "Horário já está preenchido"
```

---

## GET /agendamentos

Lista todos os agendamentos de um dia específico (00:00:00 até 23:59:59).

**Status de retorno:** `200 OK`

### Query Parameters

| Parâmetro | Tipo      | Obrigatório | Exemplo      |
|-----------|-----------|-------------|--------------|
| `data`    | LocalDate | Sim         | `2026-05-10` |

### Exemplo de requisição

```
GET /agendamentos?data=2026-05-10
```

### Resposta de sucesso

```json
[
  {
    "id": 1,
    "servico": "Corte de cabelo",
    "profissional": "João",
    "dataHoraAgendamento": "2026-05-10T10:00:00",
    "cliente": "Maria",
    "telefoneCliente": "11999999999",
    "dataInsercao": "2026-04-28T09:00:00"
  }
]
```

---

## PUT /agendamentos

Atualiza os dados de um agendamento existente, identificado por cliente e data/hora. O ID original é preservado.

**Status de retorno:** `202 Accepted`

### Query Parameters

| Parâmetro             | Tipo          | Obrigatório | Exemplo                  |
|-----------------------|---------------|-------------|--------------------------|
| `cliente`             | String        | Sim         | `Maria`                  |
| `dataHoraAgendamento` | LocalDateTime | Sim         | `2026-05-10T10:00:00`    |

### Body (JSON)

```json
{
  "servico": "Coloração",
  "profissional": "Ana",
  "dataHoraAgendamento": "2026-05-10T11:00:00",
  "cliente": "Maria",
  "telefoneCliente": "11988888888"
}
```

### Erro se não encontrado

```
RuntimeException: "Horário não está preenchido"
```

---

## DELETE /agendamentos

Remove um agendamento identificado por cliente e data/hora.

**Status de retorno:** `204 No Content`

### Query Parameters

| Parâmetro             | Tipo          | Obrigatório | Exemplo                  |
|-----------------------|---------------|-------------|--------------------------|
| `cliente`             | String        | Sim         | `Maria`                  |
| `dataHoraAgendamento` | LocalDateTime | Sim         | `2026-05-10T10:00:00`    |

### Exemplo de requisição

```
DELETE /agendamentos?cliente=Maria&dataHoraAgendamento=2026-05-10T10:00:00
```

---

## Swagger UI

A documentação interativa gerada automaticamente pelo SpringDoc OpenAPI está disponível em:

```
http://localhost:8080/swagger-ui.html
```
