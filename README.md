# Argus SOC: Mini SIEM/XDR com console web

Plataforma de monitoramento que analisa eventos de segurança, detecta ameaças por regras mapeadas ao **MITRE ATT&CK**, calcula uma **pontuação de risco** por alerta e exibe tudo em um **console web** com triagem e ações de resposta a incidentes (isolar host, bloquear IP, fechar alerta).

## Funcionalidades

- **Motor de detecção em Python** com 7 regras e pontuação de risco (0 a 100)
- **API REST (Flask)** com estado, ações de resposta e histórico
- **Console web** com Dashboard, Ações, Alertas, Computadores, Eventos e relatórios, Políticas e Administração
- Status dos computadores derivado das detecções (crítico, aviso, gerenciado, não gerenciado)
- Interface em vermelho, branco e preto, com tema claro e escuro

## Arquitetura

```
eventos (data.py) --> detecção (detection.py) --> API Flask (app.py) --> console web (static/index.html)
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

## Como executar

```bash
pip install -r requirements.txt
python app.py
```

Abra http://127.0.0.1:5000

## API

| Método | Rota | Descrição |
|---|---|---|
| GET | `/api/state` | Computadores, alertas, políticas, usuários e histórico |
| POST | `/api/actions/isolate` | Isola um host (`{"host": "srv-001"}`) |
| POST | `/api/actions/block-ip` | Bloqueia um IP (`{"ip": "185.220.101.4"}`) |
| POST | `/api/actions/scan` | Inicia varredura em um host |
| POST | `/api/alerts/<id>/close` | Fecha um alerta |
| POST | `/api/alerts/<id>/isolate` | Contém o alerta e isola o host |

## Limitações e próximos passos

- Os eventos são de exemplo (`data.py`) e o estado fica em memória. As ações são registradas, mas não executam bloqueios reais.
- Políticas e usuários são alterados só no navegador.
- Próximos passos: ingestão de logs reais (auth.log, Sysmon), banco de dados, enriquecimento com AbuseIPDB e VirusTotal, autenticação e testes automatizados.
