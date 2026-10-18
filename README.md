# Argus SOC: console de operações de segurança

Console web de SOC (Security Operations Center) inspirado em plataformas XDR, com triagem de alertas, resposta a incidentes e relatórios. A demo roda só com HTML, CSS e JavaScript, e há um backend opcional em Python com motor de detecção por regras mapeadas ao **MITRE ATT&CK**.

## Funcionalidades

- **Dashboard:** alertas por severidade, status dos computadores, eventos por hora e filtros por período e grupo
- **Alertas:** lista com detalhe, linha do tempo e ações (isolar host, bloquear IP, fechar alerta)
- **Ações:** resposta manual com validação de IP e histórico
- **Eventos e relatórios:** filtro por tipo e relatório de segurança copiável
- **Computadores:** inventário com status (crítico, aviso, gerenciado, não gerenciado)
- **Políticas:** firewall, IPS e anti-malware com modo Prevenir ou Detectar
- **Administração:** usuários e preferências, com tema claro e escuro

## Estrutura

```
argus-soc/
├── index.html          # console (demo standalone)
├── README.md
└── backend/            # opcional
    ├── app.py          # API Flask
    ├── detection.py    # regras de detecção + pontuação de risco
    ├── data.py         # eventos, ativos, políticas e usuários de exemplo
    ├── requirements.txt
    └── static/index.html
```

## Como usar

**Demo (sem instalar nada):** abra o `index.html` no navegador, ou publique com GitHub Pages.

**Com backend em Python:**

```bash
cd backend
pip install -r requirements.txt
python app.py
```

Abra o navegador na porta 5000 do seu computador (127.0.0.1:5000).

## Arquitetura (backend)

```
eventos (data.py) --> detecção (detection.py) --> API Flask (app.py) --> console web
```

## Regras de detecção

| Regra | Técnica MITRE | Severidade |
|---|---|---|
| Força bruta SSH seguida de login | T1110 Brute Force | Crítico |
| Força bruta SSH sem sucesso | T1110 Brute Force | Aviso |
| PowerShell codificado (Office como processo pai) | T1059.001 PowerShell | Crítico |
| Novo administrador local | T1136 Create Account | Aviso |
| Varredura de portas interna | T1046 Network Scan | Aviso |
| Login fora do horário | T1078 Valid Accounts | Aviso |
| Software de acesso remoto não autorizado | T1219 Remote Access Software | Aviso |
| Hash de malware conhecido | T1486 Data Encrypted for Impact | Crítico |

Cada alerta recebe uma pontuação de risco de 0 a 100, calculada pela severidade base mais fatores como número de tentativas e contexto do processo.

## API

| Método | Rota | Descrição |
|---|---|---|
| GET | `/api/state` | Computadores, alertas, políticas, usuários e histórico |
| POST | `/api/actions/isolate` | Isola um host (`{"host": "srv-001"}`) |
| POST | `/api/actions/block-ip` | Bloqueia um IP (`{"ip": "185.220.101.4"}`) |
| POST | `/api/actions/scan` | Inicia varredura em um host |
| POST | `/api/alerts/<id>/close` | Fecha um alerta |
| POST | `/api/alerts/<id>/isolate` | Contém o alerta e isola o host |

## Tecnologias

HTML5, CSS3 (variáveis e design responsivo), JavaScript, Python 3 e Flask.

## Limitações

- Os dados são fictícios. Na demo HTML ficam só no navegador e zeram ao recarregar.
- No backend, o estado fica em memória e as ações são apenas registradas, sem bloquear nada de verdade.
- Políticas e usuários são alterados só no navegador.

## Próximos passos

- Ingestão de logs reais (auth.log, Sysmon e Event Logs do Windows)
- Banco de dados para persistir alertas e histórico
- Enriquecimento de IPs e hashes com AbuseIPDB e VirusTotal
- Autenticação e testes automatizados

## Aviso

Projeto de estudo e portfólio, sem vínculo com nenhum fabricante de segurança. Nomes de produtos de terceiros pertencem aos seus respectivos donos.
