# Changelog

Todas as mudanças notáveis deste projeto são documentadas aqui.
O formato é baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.0.0/).

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
