# sesman makefile

SESMANOBJ = sesman.o config.o sig.o session.o env.o \
            os_calls.o d3des.o list.o file.o log.o access.o \
            scp.o scp_v0.o scp_v1.o thread.o lock.o

SESSVCOBJ = sessvc.o os_calls.o

DESTDIR = /usr/local/xrdp
CFGDIR = /etc/xrdp
PIDDIR = /var/run
MANDIR = /usr/local/man
DOCDIR = /usr/doc/xrdp

DEFINES = -DSESMAN_CFG_FILE=\"$(CFGDIR)/sesman.ini\" \
	  -DSESMAN_PID_FILE=\"$(PIDDIR)/sesman.pid\" \
	  -DSESMAN_SESSVC_FILE=\"sessvc\"

CFLAGS = -Wall -O2 -I../common -I/usr/include/nptl -I./libscp $(DEFINES)
LDFLAGS = -L/usr/gnu/lib -L/usr/lib/nptl -L./libscp -Wl,-rpath,. -lpthread -ldl -lscp
C_OS_FLAGS = $(CFLAGS) -c
CC = gcc

all: libscp_ pam tools

nopam: libscp_ no-pam tools

kerberos: libscp_ kerberos-base tools

no-pam: $(SESMANOBJ) verify_user.o
	$(CC) $(LDFLAGS) -o sesman $(SESMANOBJ) verify_user.o -lcrypt

pam: $(SESMANOBJ) verify_user_pam.o
	$(CC) $(LDFLAGS) -o sesman $(SESMANOBJ) verify_user_pam.o -lpam

pam_userpass: $(SESMANOBJ) verify_user_pam_userpass.o
	$(CC) $(LDFLAGS) -o sesman $(SESMANOBJ) verify_user_pam_userpass.o -lpam -lpam_userpass

kerberos-base: $(SESMANOBJ) verify_user_kerberos.o
	$(CC) $(LDFLAGS) -o sesman $(SESMANOBJ) verify_user_kerberos.o -lkrb5

sessvc: $(SESSVCOBJ)
	$(CC) $(LDFLAGS) -o sessvc $(SESSVCOBJ) 

tools: sessvc
	make -C tools

libscp_:
	make -C libscp

clean:
	rm -f $(SESMANOBJ) verify_user.o verify_user_pam.o verify_user_pam_userpass.o sesman sesrun.o sesrun sessvc.o sessvc
	make -C tools clean
	make -C libscp clean

install:
	install sesman $(DESTDIR)/sesman
	install startwm.sh $(DESTDIR)/startwm.sh
	install sesman.ini $(CFGDIR)/sesman.ini
	install sessvc $(DESTDIR)/sessvc
	make -C tools install
	make -C libscp install

installdeb:
	install sesman $(DESTDIRDEB)/usr/lib/xrdp/sesman
	install startwm.sh $(DESTDIRDEB)/usr/lib/xrdp/startwm.sh
	install sesman.ini $(DESTDIRDEB)/etc/xrdp/sesman.ini

os_calls.o: ../common/os_calls.c
	$(CC) $(C_OS_FLAGS) ../common/os_calls.c

d3des.o: ../common/d3des.c
	$(CC) $(C_OS_FLAGS) ../common/d3des.c

list.o: ../common/list.c
	$(CC) $(C_OS_FLAGS) ../common/list.c

file.o: ../common/file.c
	$(CC) $(C_OS_FLAGS) ../common/file.c

log.o: ../common/log.c
	$(CC) $(C_OS_FLAGS) -DLOG_ENABLE_THREAD ../common/log.c
