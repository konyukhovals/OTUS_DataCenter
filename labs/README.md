# Зарисовки по имплеменатции образов

sudo mkdir -p /opt/unetlab/addons/qemu/veos-4.29.2F
sudo tar -xvzf /home/pnetlab/veos-4.29.2F.tgz -C /opt/unetlab/addons/qemu/veos-4.29.2F
cd /opt/unetlab/addons/qemu/veos-4.29.2F
sudo mv *.qcow2 hda.qcow2
sudo /opt/unetlab/wrappers/unl_wrapper -a fixpermissions



sudo mkdir -p /opt/unetlab/addons/qemu/vios-adventerprisek9-m.SPA.159-3.M6
sudo tar -xvzf /home/pnetlab/vios-adventerprisek9-m.SPA.159-3.M6.tgz -C /opt/unetlab/addons/qemu/vios-adventerprisek9-m.SPA.159-3.M6
cd /opt/unetlab/addons/qemu/vios-adventerprisek9-m.SPA.159-3.M6
sudo mv *.qcow2 hda.qcow2
sudo /opt/unetlab/wrappers/unl_wrapper -a fixpermissions


# Вход в Arista

admin
en
zerotouch cancel

en
conf t
ip routing
exi
wr



