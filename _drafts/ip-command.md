## ip

�鿴������Ϣ
ip -s -s link ls eth0

�����͹ر��豸
ip link set dev ethx up/down

Ϊĳ����������ip
ip addr add xx dev ethx

��ʾ������ip��ַ
ip addr show ethx

�ı�mtu��ֵ
ip link set dev eth0 mtu xxxx

�޸�mac��ַ
ip link set dev eth0 address xx:xx:xx:xx:xx:xx

�������ip��ַ
ip addr flush ethx

����Ĭ��·��
ip route add default via xx.xx.xx.xx [dev ethx]

ɾ��Ĭ��·��
ip route del default via xx.xx.xx.xx [dev ethx]

��ĳ����������·��
ip route add xxxx/yy via zz dev ethx

ɾ��ĳ�����ص�·��
ip route del xxxx/yy via zz dev ethx

��ĳ����������·��
ip route add x via y dev eth0

ɾ��ĳ��������·��
ip route add x via y dev eth0



## netstat

��ʾ�������ӣ�·�ɱ��ӿ�״̬��αװ���ӣ�������·��Ϣ���鲥��Ա��
����
-a ��ʾ���м����Ķ˿�
-s ��ʾ����Э���ͳ����
-r ��ʾ�ں�·����Ϣ
-i  ��ʾ����ӿ��б�

��ʾ����ӿ��б�
netstat -i

��ʾ����Э���ͳ��ֵ
netstat -s

��ʾ�ں�·�ɱ�
netstat -r

�鿴tcp�˿�״̬
netstat -tap

## ����

	1.������ʲô���͵ı��ġ�
	2.��ʲô�ط����ı��ġ�
	3.Ϊʲô�ᶪ����

Ӳ����������
ethtool -S ethx

��·�㣨���㶪����
ifconfig

ip�㣨���㣩
netstat -s�鿴����ͳ��

udp�㣨�Ĳ㣩
netstat -s �鿴����ͳ��

tcp�㣨�Ĳ㣩
netstat -s eth0�鿴����ͳ��

ethtool -k ethx
	tcp-segmentation-offload: on
	udp-fragmentation-offload: on


## arping
	--��ָ�������Ϸ���ARP����ָ����ַ��Դ��ַ ��-s�� ����ָ����������ֱ�� ping MAC ��ַ���Լ��ҳ���Щ ip ��ַ����Щ������ʹ����
����
	-b ���ֹ㲥
	-f �õ���һ���ظ��� �˳�
	-q ����ʾ������Ϣ
	-U ������ARPģʽ�������ھ�
	-c <���ݰ�����Ŀ> ���͵����ݰ�����Ŀ
	-w <��ʱʱ��> ���ó�ʱʱ��
	-I <����> ʹ��ָ������̫���豸��Ĭ�������ʹ��eth0
	-s ָ��ԴIP��ַ
	-h ��ʾ������Ϣ
	-V ��ʾ�汾��Ϣ

