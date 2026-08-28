# Mikrodomo

**Seu mordomo MikroTik para configurações fáceis.**

Mikrodomo é um gerador de configuração MikroTik (`.rsc`) que roda 100% no navegador — sem backend, sem enviar dados pra nenhum servidor. Ele monta failover entre até 3 links WAN (PPPoE, DHCP ou IP estático, com ou sem VLAN), filtros de bloqueio de sites por grupo ou para a rede toda, QoS por grupo, e consegue **importar um `.rsc` já existente** para pré-preencher os campos em vez de começar do zero.

> ⚠️ **Esta aplicação não é recomendada para uso de iniciantes.** Ela gera comandos que alteram roteamento, firewall e NAT em produção — tenha certeza do que está fazendo antes de aplicar a configuração num MikroTik real.

## ✨ Funcionalidades

- **Failover multi-WAN** (até 3 links) com `check-gateway=ping`, suporte a PPPoE / DHCP / IP estático e VLAN.
- **Filtros de bloqueio** por grupo nomeado (com lista de IPs própria) ou para a rede inteira: Meta, Spotify, YouTube, Netflix, TikTok, Twitter/X, Discord, Twitch, Steam, ou sites personalizados.
- **Anti-bypass de DNS**: bloqueio de DoH (SNI + IPs de provedores conhecidos), DoT, QUIC, e opção de forçar todo mundo a usar o DNS do próprio roteador.
- **QoS por grupo** (limite de upload/download via mangle + queue tree).
- **Importação de `.rsc` existente**: detecta e pré-preenche links WAN (inclusive por dedução, quando o arquivo não segue um padrão de nomenclatura específico), bridge LAN já existente, DDNS (MikroTik Cloud), grupos de bloqueio já configurados (inclusive no formato legado baseado em `tls-host`), e evita duplicar address-lists que já existem no arquivo.
- **Verificação prévia** antes de gerar: avisa sobre nomes de grupo duplicados, colisão com listas já existentes, grupos sem IP cadastrado, interfaces reaproveitadas em dois links, entre outros.
- Tudo que não é reconhecido no `.rsc` importado vai para uma caixa de **"Outras Configurações"**, editável, e é preservado ao exportar de novo — nada se perde.

## 🚀 Como usar

1. Abra o `index.html` em qualquer navegador (não precisa de servidor — é um arquivo estático único).
2. *(Opcional)* Importe um `.rsc` já existente para pré-preencher os campos.
3. Preencha/ajuste identificação, links WAN, rede LAN, grupos de bloqueio e QoS.
4. Clique em **Gerar Configuração MikroTik**, revise os avisos da verificação prévia (se houver), e copie/baixe o `.rsc` gerado.
5. Aplique no MikroTik com cautela — sempre faça backup antes (`/export` e `/system backup save`).

## 🖥️ Rodando localmente / GitHub Pages

Não há build step. Para servir via GitHub Pages, ative Pages apontando para a branch `main` (raiz) — o `index.html` já está pronto pra isso.

## 📄 Licença

Este projeto está licenciado sob a [MIT License](LICENSE).

## 👤 Autor

Desenvolvido por [alctechsolutions@proton.me](mailto:alctechsolutions@proton.me)

## 📝 Changelog

Veja [CHANGELOG.md](CHANGELOG.md).
