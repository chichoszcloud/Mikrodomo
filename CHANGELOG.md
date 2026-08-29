# Changelog

Todas as mudanças notáveis deste projeto são documentadas aqui.
O formato é baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.0.0/).

## [1.0.2] - 2026-08-29

### Corrigido
- Valor padrão do campo "Rede LAN" estava em `/23` (`192.168.10.0/23`), resquício do protótipo inicial da ferramenta e inconsistente com o placeholder de exemplo (`192.168.1.0/24`). Agora o valor padrão é `/24`.

## [1.0.1] - 2026-08-29

### ⚠️ Correções de segurança / risco de perda de acesso

Esta versão nasceu de uma comparação real entre um `.rsc` de produção e o resultado gerado pelo Mikrodomo a partir dele, que revelou riscos sérios de o equipamento ficar inacessível remotamente após aplicar a configuração gerada. Todos os pontos abaixo foram corrigidos.

- **SSH era desabilitado automaticamente** (`set ssh disabled=yes`) mesmo quando o arquivo original tinha SSH funcional configurado — corrigido: o bloco `/ip service` deixou de ser gerado incondicionalmente.
- **PPPoE duplicado**: ao reimportar um arquivo, o Mikrodomo criava uma nova interface PPPoE com nome diferente do original (`pppoe-VIVO` em vez de `pppoe-out2`, por exemplo), deixando duas sessões disputando a mesma credencial/porta — corrigido: o nome original agora é reaproveitado por padrão.
- **Rota/netwatch quebrados**: quando o arquivo original usava `gateway=<nome-da-interface>` (mecanismo válido do RouterOS para PPPoE, sem IP fixo), o Mikrodomo tratava esse texto como se fosse um IP de monitoramento, gerando uma rota recursiva referenciando uma interface renomeada/inexistente — corrigido: detecta esse padrão e mantém o mecanismo original.
- **Gateway de link estático vazio** quando detectado por dedução (ex: `add dst-address=X gateway= scope=10`) — corrigido: quando não há um "gateway real" separado do IP de monitoramento, gera a rota direta em vez de uma linha quebrada.
- **Prefixo de rede da bridge fixo em `/23`**, ignorando o CIDR real informado ou importado (ex: gerava `/23` mesmo para uma rede `/24`) — corrigido.
- **Identidade do sistema sem aspas** quando o nome tinha espaço (`set name=Fulano da Silva` em vez de `set name="Fulano da Silva"`) — corrigido.
- **Perda silenciosa de dados reconhecidos no import**: comentários de porta física (ex: `ether1 = Port_dedicada_Link_Vivo`) e a definição do túnel `/interface sstp-client` (usado para gestão remota) eram identificados pelo parser mas nunca reproduzidos em lugar nenhum, nem em "Outras Configurações" — corrigido: agora são sempre preservados no export.

### Adicionado

- **Painel "Recomendações do Mikrodomo"**: dois pontos que antes eram aplicados automaticamente agora são opt-in via checkbox, desmarcados por padrão:
  - Renomear interfaces PPPoE para o padrão do Mikrodomo (`pppoe-OPERADORA`) — desmarcado reaproveita o nome original.
  - Aplicar hardening padrão de acesso (desabilitar FTP/Telnet/WWW/API, restringir Winbox à LAN) — desmarcado não gera nenhum bloco `/ip service`.
- **Garantia explícita**: SSH, Winbox, usuários e grupos (`/user`, `/user group`) nunca são gerados ou alterados pela lógica do Mikrodomo, esteja o painel de recomendações marcado ou não — se existiam no arquivo original, permanecem exatamente como estavam, em "Outras Configurações".
- Portas de bridge (LAN) detectadas no import agora populam automaticamente o campo "Interfaces LAN" e são recriadas na configuração gerada (antes eram descobertas mas nunca usadas).
- Gateway real da LAN, quando detectado no `.rsc` importado, é usado em vez de recalculado (evita divergência se o gateway original não terminar em `.1`).

## [1.0.0] - 2026-08-27

### Adicionado
- Rebrand completo para **Mikrodomo** (antes: "Gerador Failover MikroTik" / "Clube de Rede").
- Importação de `.rsc` com detecção em dois passos para links WAN: primeiro por palavra-chave no comentário (PRINCIPAL/BACKUP-1/BACKUP-2), e se não encontrar, por correlação de rótulo (clustering) cruzando `/interface ethernet`, `/ip address`, `/ip dhcp-client`, `/interface pppoe-client`, `/interface vlan` e `/ip route` (rankeando por distância da rota default).
- Campos de interface (Principal/Backup-1/Backup-2) viraram dropdown, populado automaticamente com as interfaces detectadas no `.rsc` importado.
- Campo de nome da Bridge LAN — detecta e reaproveita uma bridge já existente (ex: `Br_Rede`) em vez de criar uma duplicada.
- Detecção de address-lists/grupos de bloqueio já existentes no arquivo importado, para evitar duplicação e colisão de nomes.
- Reconhecimento do padrão legado de bloqueio baseado em `tls-host` + `add-dst-to-address-list` (usado antes da adoção de `layer7-protocol`), mapeando para os mesmos sites do catálogo atual.
- Aviso explícito quando um site importado vinha de uma regra que estava `disabled=yes` no arquivo original, já que a nova configuração gerada sairia com o bloqueio ativo.
- Campo de DDNS (MikroTik Cloud) ao lado da identificação do sistema/cliente.
- QoS (controle de banda upload/download) por grupo de bloqueio, via mangle + queue tree.
- Verificação prévia (sanity check) antes de gerar a configuração final: nomes de grupo duplicados, colisão com lista existente, grupo sem IP cadastrado, interface reaproveitada em mais de um link.
- Banner de aviso "não recomendado para uso de iniciantes".
- Filtros de bloqueio por grupo nomeado (com lista de IPs própria) ou para a rede inteira, com catálogo de sites (Meta, Spotify, YouTube, Netflix, TikTok, Twitter/X, Discord, Twitch, Steam) e opção de site personalizado.
- Bloqueio de DoH (SNI + IPs conhecidos), DoT, QUIC, e opção de forçar DNS do roteador, por grupo/rede toda.
- Caixa de "Outras Configurações": qualquer trecho do `.rsc` importado sem campo específico é preservado e editável, e volta a ser incluído no export.

### Corrigido
- Falso positivo: uma regra de NAT desabilitada com lista negada (ex: `src-address-list=!Liberados_Bloqueios`) estava sendo interpretada incorretamente como um grupo com "Forçar DNS" ativo.
