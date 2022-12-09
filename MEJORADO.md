import re 
import netmiko
import uuid


cisco1 = {
    "device_type": "cisco_ios",
    "host": "192.168.1.3",
    "username": "cisco",
    "password": "cisco",
    }

net_connect = netmiko.ConnectHandler(**cisco1)
print("conexion exitosa")
shrun = net_connect.send_command("show version")
print("informacion del dispositivo: ")
print(shrun)
shrun = net_connect.send_command("show cdp neig detail")
print(shrun)
print ("La dirección MAC en forma formateada y menos compleja es : ", end="")
print (':'.join(re.findall('..', '%012x' % uuid.getnode())))

shrun = net_connect.send_command("show ip interface brief")
print(shrun)

#configurar una interfaz
'''
config_commands = [
    "interface fas1/0/1",
    "no switchport",
    "ip address 192.168.0.1 255.255.255.0",
    "no shutdown"]
print("------"*20)
output = net_connect.send_config_set(config_commands)
print(output)
print("------"*20)
#verificar la configuracion
shrun = net_connect.send_command("show run")
print(shrun)
'''


def BUSQUEDA(DICCIONARIO,MAC,IP):
    TABLEINFO=""
    MACLIST=()
    PORTLIST=()
    IPLIST=()
    STRPORT=""
    print("conectando con "+IP)
    DISPOSITIVO_CONEXION= netmiko.ConnectHandler(**DICCIONARIO) 
    print("conexion exitosa!")
    TABLEINFO= CONEXION.send_command("show mac address-table")
    MACLIST = re.findall("\w+\w+\w+\w+.\w+\w+\w+\w+.\w+\w+\w+\w+",TABLEINFO)
    '''print("--------------------------------------------------------------------------------------")
    print("LISTA DE MAC ADDRESS DEL NODO "+IP)
    for a in range(len(MACLIST)):
        print(MACLIST[a])
    print("--------------------------------------------------------------------------------------")'''
    TABLEINFO= DISPOSITIVO_CONEXION.send_command("show mac address-table address "+MAC)
    print("show mac address-table address "+MAC)
    print(TABLEINFO)
    PORTLIST=re.findall("\w+\/+\w+(\/+\w+)?",TABLEINFO)
    print("Puertos en los que se encuentra el nodo")
    for a in range(len(PORTLIST)):
        print(PORTLIST[a])
    print("--------------------------------------------------------------------------------------")
    if (len(PORTLIST))==0:
        print("En este dispositivo no se encuentra la direccion mac: "+MAC)
    else:
        STRPORT=PORTLIST[0]
        TABLEINFO=DISPOSITIVO_CONEXION.send_command("show cdp neighbor "+STRPORT[0]+" detail")
        print("show cdp neighbor "+STRPORT[0]+" detail")
        if len(TABLEINFO)==0:
            print("La direccion: "+MAC+" esta en la interfaz "+STRPORT[0]+" del switch: "+IP)
        else:
            IPLIST=re.findall("[0-9]\w+\.+[0-9]\w+\.+[0-9]\w+\.+[0-9]\w+",TABLEINFO)
            IP=IPLIST[0]
            NUEVA_CONEXION={
            'device_type':'cisco_ios',
            'host':IP,
            'username':'cisco',
            'password':'cisco'}
            BUSQUEDA(NUEVA_CONEXION,MAC,IP)
    pass

print("BIENVENIDO A NUESTRA BUSQUEDA DE NODOS :D")
MACADDRESS= input("direccion mac que quieres buscar: ")
print("vamos a empezar.")

DIRECCIONIP='192.168.1.1'
CONEXION={
    'device_type':'cisco_ios',
    'host':DIRECCIONIP,
    'username':'cisco',
    'password':'cisco'}

BUSQUEDA(CONEXION,MACADDRESS,DIRECCIONIP)
