Informções sobre as licenças
============================

Este programa é um software livre: você pode redistribuí-lo e/ou modificá-lo
sob os termos da GNU Affero General Public License como
publicado pela Free Software Foundation versão 3 conforme publicado por
a Fundação do Software Livre. Você não pode usar, modificar ou distribuir
este programa sob qualquer outra versão do GNU Affero General Public
Licença.

Este programa é distribuído na esperança de que seja útil,
mas SEM QUALQUER GARANTIA; sem mesmo a garantia implícita de
COMERCIALIZAÇÃO ou ADEQUAÇÃO A UM DETERMINADO FIM. Veja o
Licença Pública Geral GNU Affero para mais detalhes.

Você deve ter recebido uma cópia da Licença Pública Geral GNU Affero
junto com este programa. Caso contrário, consulte <http://www.gnu.org/licenses/>.

Contribuições/remendos
=====================

Se você decidir participar do projeto OdinMS, por favor, poste seu
contribuições na forma de um arquivo de patch no fórum oficial do desenvolvedor OdinMS
localizado em <http://www.odinms.de/forum/>.

Requisitos
============

OdinMS requer uma JVM compatível com o Java SE 6.0. A Sun JVM pode ser
adquirido em <http://java.sun.com/javase/downloads/index.jsp>.

Se você estiver usando a Sun JVM, os arquivos JCE Unlimited Strength
é requerido. <http://java.sun.com/javase/downloads/index.jsp>

Você também precisará de algumas bibliotecas:
- Apache MINA 1.1 <http://mina.apache.org/downloads.html>
- MySQL Connector/J <http://dev.mysql.com/downloads/connector/j/>
- slf4j 1.5 <http://www.slf4j.org/download.html>

Finalmente, você também precisará dos arquivos de dados Maple Story (*.wz) disponíveis.

Para fins de armazenamento, o OdinMS requer um back-end de banco de dados acessível
usando um driver JDBC. Como outros DBMS não foram testados com OdinMS,
recomendo o MySQL. <http://dev.mysql.com>

Conceito de arquitetura OdinMS
===========================

     Servidor Mundial
     / | \
  Entrar Canal1 Canal2

O servidor OdinMS Maple Story é dividido em três partes:
- O servidor de login lida com a autenticação do usuário e interage com o
cliente nos estágios iniciais do jogo (seleção de servidor etc.)
- Na verdade, o servidor de canal hospeda um canal Maple Story e lida com
a maior parte do jogo.
- O servidor mundial é responsável pela comunicação entre canais e
fornece interfaces para que os servidores de canal e login interajam entre si.

A comunicação entre os servidores de login, canal e mundo é implementada usando
RMI. Portanto, é possível implantar o OdinMS em um ambiente distribuído.
Também é possível executar vários servidores de canal dentro de um único processo, então
eles podem compartilhar recursos, especialmente no que diz respeito ao acesso a arquivos de dados.

Observe: O acesso ao banco de dados não é gerenciado pelo servidor mundial de forma alguma.
Cada servidor de canal deve ter acesso ao mesmo banco de dados. (Ao usar MySQL este
pode ser alcançado simplesmente usando conexões remotas ou uma solução MySQL Cluster)

Opções de JVM Necessárias
====================

Para executar o servidor OdinMS diferente, você terá que fornecer algumas definições para a JVM:

net.sf.odinms.recvops
- Caminho para o arquivo recvops.properties contendo os códigos de operação do protocolo
net.sf.odinms.sendops
- Caminho para o arquivo sendops.properties contendo os códigos de operação do protocolo
net.sf.odinms.login.config
- Caminho para o arquivo login.properties contendo informações de configuração
para o servidor de login
net.sf.odinms.channel.config
- Caminho para o arquivo channel.proerties contendo informações de configuração
para o(s) servidor(es) de canal
net.sf.odinms.wzpath
- Caminho para a pasta que contém os arquivos Maple Story .wz

Arquivos de configuração
===================

Para obter informações detalhadas sobre as várias opções configuráveis, consulte o
comentários incluídos nos próprios arquivos de configuração.
Além dos arquivos mencionados nas opções da JVM, a seguinte configuração
arquivos são necessários no diretório de trabalho do servidor:

Em todos os servidores:
db.propriedades
- Contém informações e credenciais para a conexão com o banco de dados

No servidor mundial:
propriedades.mundo
- Contém informações de configuração relacionadas ao jogo (válido para todos
canais conectados a este servidor mundial)

As alterações de configuração relacionadas à jogabilidade para canais únicos podem ser feitas no
banco de dados usando a tabela channelconfig.

Armazenamentos de chaves
=========

OdinMS usa criptografia SSL para a comunicação RMI entre mundo, canal e
servidores de login. Você terá que gerar um par de chaves pública/privada para cada servidor
e criar armazenamentos de chave e confiança apropriados.

Você pode criá-los e gerenciá-los usando o aplicativo keytool fornecido com o JDK
http://java.sun.com/javase/6/docs/technotes/tools/windows/keytool.html

Caminhos e senhas para os armazenamentos de chave e confiança podem ser fornecidos usando o 
seguinte A JVM define:
javax.net.ssl.keyStore
javax.net.ssl.keyStorePassword
javax.net.ssl.trustStore
javax.net.ssl.trustStorePassword

Comece
=======

Um exemplo de configurações de JVM é fornecido nos arquivos launch_*.sh/launch_*.bat de
o lançamento.

Ordem de lançamento:
1. Servidor Mundial
2. Servidor de Login
3. Servidor(es) de Canal

Como o servidor mundial gerencia todas as interações entre servidores e hospeda o RMI
registro, você terá que iniciá-lo primeiro. Depois disso, o servidor de login deve ser iniciado.
Se você tentar registrar um servidor de canal quando nenhum servidor de login estiver ativo, você 
poderá encontrar problemas.
