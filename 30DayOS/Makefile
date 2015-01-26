OBJS_BOOTPACK = bootpack.obj naskfunc.obj hankaku.obj graphic.obj dsctbl.obj \
		int.obj fifo.obj keyboard.obj mouse.obj memory.obj sheet.obj timer.obj \
		mtask.obj window.obj console.obj file.obj


TOOLPATH = ../z_tools/
INCPATH  = ../z_tools/haribote/

MAKE     = $(TOOLPATH)make.exe -r
NASK     = $(TOOLPATH)nask.exe
CC1      = $(TOOLPATH)cc1.exe -I$(INCPATH) -Os -Wall -quiet
GAS2NASK = $(TOOLPATH)gas2nask.exe -a
OBJ2BIM  = $(TOOLPATH)obj2bim.exe
MAKEFONT = $(TOOLPATH)makefont.exe
BIN2OBJ  = $(TOOLPATH)bin2obj.exe
BIM2HRB  = $(TOOLPATH)bim2hrb.exe
RULEFILE = $(TOOLPATH)haribote/haribote.rul
EDIMG    = $(TOOLPATH)edimg.exe
IMGTOL   = $(TOOLPATH)imgtol.com
COPY     = copy
DEL      = del

# ����Ĭ������Ϊ����img
default :
	$(MAKE) img

# ����ִ�й���
# ��ipl10.nas����Ϊipl10.bin�ļ�
ipl10.bin : ipl10.nas Makefile
	$(NASK) ipl10.nas ipl10.bin ipl10.lst

# ��asmhead.asm����Ϊasmhead.bin
asmhead.bin : asmhead.nas Makefile
	$(NASK) asmhead.nas asmhead.bin asmhead.lst

# ���ֿ�hankaku.txt����Ϊhankaku.bin
hankaku.bin : hankaku.txt Makefile
	$(MAKEFONT) hankaku.txt hankaku.bin

# ���ֿ�ת��Ϊ.obj
hankaku.obj : hankaku.bin Makefile
	$(BIN2OBJ) hankaku.bin hankaku.obj _hankaku

# ��OBJS_BOOTPACK��obj����Ϊ.bimӳ���ļ��������ǰ����������OBJS_BOOTPACK�����Ի��Զ�ִ�а�naskfunc.nas������.c�ļ�����Ϊobj�ļ�
bootpack.bim : $(OBJS_BOOTPACK) Makefile
	$(OBJ2BIM) @$(RULEFILE) out:bootpack.bim stack:3136k map:bootpack.map \
		$(OBJS_BOOTPACK)
# 3MB+64KB=3136KB

# ��bootpack.bimӳ�����Ϊ.hrb�ļ�
bootpack.hrb : bootpack.bim Makefile
	$(BIM2HRB) bootpack.bim bootpack.hrb 0

# ��Ӧ�ó���hlt.nasת��Ϊhrb�ļ�
hlt.hrb : hlt.nas Makefile
	$(NASK) hlt.nas hlt.hrb hlt.lst
	
# ��.hrbӳ���asmhead.bin �ϲ�����haribote.sysϵͳ�ļ�
haribote.sys : asmhead.bin bootpack.hrb Makefile
	copy /B asmhead.bin+bootpack.hrb haribote.sys

# ��ipl10.bin��haribote.sys������ɲ���ϵͳ�ļ�haribote.img
# ��ipl10.nas  make.bat hlt.hrb ��3���ļ�Ҳ����ȥ
haribote.img : ipl10.bin haribote.sys hlt.hrb Makefile
	$(EDIMG)   imgin:../z_tools/fdimg0at.tek \
		wbinimg src:ipl10.bin len:512 from:0 to:0 \
		copy from:haribote.sys to:@: \
		copy from:ipl10.nas to:@: \
		copy from:make.bat to:@: \
		copy from:hlt.hrb to:@: \
		imgout:haribote.img

# ��c�ļ����б���
# ��.c�ļ�����Ϊ.gas�ļ�����Ҫ.c bootpack.h �� Makefile�ļ���Ȼ�����cc1.exe���д���
%.gas : %.c bootpack.h Makefile
	$(CC1) -o $*.gas $*.c

# ��.gas�ļ�ת��Ϊ.nas�ļ����������������˵��
%.nas : %.gas Makefile
	$(GAS2NASK) $*.gas $*.nas

# ��.nasתΪ.obj�ļ������.lst�ļ�
%.obj : %.nas Makefile
	$(NASK) $*.nas $*.obj $*.lst

# ���ݲ�ͬ������ֱ�ִ������������Ĭ��Ϊimg
img :
	$(MAKE) haribote.img

run :
	$(MAKE) img
	$(COPY) haribote.img ..\z_tools\qemu\fdimage0.bin
	$(MAKE) -C ../z_tools/qemu

install :
	$(MAKE) img
	$(IMGTOL) w a: haribote.img

clean :
	$(DEL) *.bin
	$(DEL) *.lst
	$(DEL) *.obj
	$(DEL) bootpack.map
	$(DEL) bootpack.bim
	$(DEL) bootpack.hrb
	$(DEL) haribote.sys

src_only :
	$(MAKE) clean
	$(DEL) haribote.img
	$(DEL) *.hrb