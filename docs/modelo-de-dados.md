# Modelo de Dados

## Entidade: Agendamento

Tabela no banco: `agendamento`

| Campo                  | Tipo          | Restrições              | Descrição                             |
|------------------------|---------------|-------------------------|---------------------------------------|
| `id`                   | Long          | PK, auto-increment      | Identificador único                   |
| `servico`              | String        |                         | Nome do serviço a ser prestado        |
| `profissional`         | String        |                         | Nome do profissional responsável      |
| `dataHoraAgendamento`  | LocalDateTime |                         | Data e hora do atendimento            |
| `cliente`              | String        |                         | Nome do cliente                       |
| `telefoneCliente`      | String        |                         | Telefone de contato do cliente        |
| `dataInsercao`         | LocalDateTime | Default: `now()`        | Timestamp de criação do registro      |

## Mapeamento JPA

```java
@Entity
@Table(name = "agendamento")
public class Agendamento {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String servico;
    private String profissional;
    private LocalDateTime dataHoraAgendamento;
    private String cliente;
    private String telefoneCliente;
    private LocalDateTime dataInsercao = LocalDateTime.now();
}
```

## Queries customizadas (Repository)

| Método                                              | Descrição                                                    |
|-----------------------------------------------------|--------------------------------------------------------------|
| `findByServicoAndDataHoraAgendamentoBetween`        | Busca conflito de horário para um serviço em intervalo       |
| `deleteByDataHoraAgendamentoAndCliente`             | Remove agendamento por data/hora e cliente (transacional)    |
| `findByDataHoraAgendamentoBetween`                  | Lista agendamentos dentro de um intervalo de data/hora       |
| `findByDataHoraAgendamentoAndCliente`               | Busca agendamento específico por data/hora e cliente         |

## Banco de dados

- **Tipo:** H2 in-memory
- **URL JDBC:** `jdbc:h2:mem:agendamentos-db`
- **DDL:** Gerenciado automaticamente pelo Hibernate (`ddl-auto=update`)
- **Console web:** `http://localhost:8080/h2-console`
- **Obs.:** Os dados são perdidos ao reiniciar a aplicação por ser in-memory.
