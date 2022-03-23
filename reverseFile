code_seg segment
        ASSUME  CS:CODE_SEG,DS:code_seg,ES:code_seg
	org 100h
;==============================================
CR		EQU		13
LF		EQU		10
Space	EQU		20h
;==============================================
print_letter	macro	letter
	push	AX
	push	DX
	mov	DL, letter
	mov	AH,	02
	int	21h
	pop	DX
	pop	AX
endm
;==============================================
print_mes	macro	message
	local	msg, nxt
	push	AX
	push	DX
	mov	DX, offset msg
	mov	AH,	09h
	int	21h
	pop	DX
	pop	AX
	jmp nxt
	msg	DB message,'$'
	nxt:
	endm

printCRLF macro
	print_letter CR
	print_letter LF
endm
;==============================================
start:
    mov 	SI,		80h      	
    mov 	AL,		byte ptr[SI]
    cmp 	AL,		0
    jne 	cont1
;==============================================
	print_mes	'Parent file name > '	
	mov		AH,	0Ah
	mov		DX,	offset	FileName
	int		21h
	xor	BH,	BH
	mov	BL,  FileName[1]
	mov	FileName[BX+2],	0
	mov	AX,	3D02h		; Open file for read/write
	mov	DX, offset FileName+2
	int	21h
	jnc FirstOpenOkFromInput
	printCRLF
	print_mes	'Parent file open error: '
	call print_reg_AX
	int	20h
	cont1:
	jmp ConsoleInp
;==============================================
FirstOpenOkFromInput:
	mov Handler1, AX
	printCRLF
	print_mes	'Parent openOK'
	printCRLF
	jmp ChildInput
;==============================================
ChildInput:
	printCRLF
	print_mes	'Child file name > '	
	mov		AH,	0Ah
	mov		DX,	offset	FileName
	int		21h
	xor	BH,	BH
	mov	BL,  FileName[1]
	mov	FileName[BX+2],	0
	mov	AX,	3D02h		; Open file for read/write
	mov	DX, offset FileName+2
	int	21h
	jnc SecondOpenOkFromInput
	printCRLF
	print_mes	'Child file open error: '
	call print_reg_AX
	int	20h
;==============================================
SecondOpenOkFromInput:
	mov Handler2, AX
	printCRLF
	print_mes	'Child openOK'
	printCRLF
	jmp WorkingWithFiles
;==============================================
FirstOpenOkFromConsole:
	mov Handler1, AX
	printCRLF
	print_mes	'Parent openOK'
	jmp ChildInput

ConsoleInp:
	xor	BH,	BH
	mov	BL, ES:[80h]		;  а вот так -> mov	BL, [80h]нельзя!!!!  
	mov	byte ptr [BX+81h],	0
	xor BX, BX
	xor CX, CX
	mov CL, ES:[80h]
metka:
	cmp		byte ptr [BX+82h], 020h
	je 		FoundSecondInConsole
	inc 	BL
loop metka
	mov		AX,	3D02h		; Open file for read/write
	mov		DX, 82h
	int		21h
	jnc	FirstOpenOkFromConsole
	printCRLF
	print_mes	'Parent file opening error: '
	mov		AX,	4C00h
	int 	21h
;=============================================
FoundSecondInConsole:
	inc BL
	mov byte ptr [BX+81h], 0
	push BX
	mov	AX,	3D02h		; Open file for read/write
	mov	DX, 82h
	int	21h
	jnc	openOk2
	printCRLF
	print_mes	'Parent file opening error: '
	call print_reg_AX
	mov		AX,	4C00h
	int 	21h
;=============================================
openOk2:
	mov Handler1, AX
	printCRLF
	print_mes	'Parent openOK'
	printCRLF
	pop BX
	mov	AX,	3D02h		; Open file for read/write
	mov DX, 82h
	add DX, BX
	int	21h
	jnc	SecondOpenOkFromConsole
	printCRLF
	print_mes	'Child file opening error: '
	call print_reg_AX
	mov		AX,	4C00h
	int 	21h
;==============================================
SecondOpenOkFromConsole:
	mov Handler2, AX
	print_mes	'Child openOK'
	jmp WorkingWithFiles
;==============================================
fix1:
	; mov byte ptr BufOut[DI], 0Ah
	; inc SI
	; dec DI
	mov al, 0Ah
	jmp cntfix
;==============================================
fix2:
	;mov byte ptr BufOut[DI], 0Dh
	;inc SI
	;dec DI
	mov	al, 0Dh
	jmp cntfix
;==============================================
WorkingWithFiles:
	mov AH, 3Fh
	mov BX, Handler1
	mov CX, 2048
	mov DX, offset BufIn
	int 21h
	mov CX, AX
	push CX
	xor SI, SI
	xor DI, DI
	mov DI, CX
	dec DI
metka3:
	mov AL, byte ptr BufIn[SI]
	cmp AL, 0Dh
	je fix1
	cmp AL, 0Ah
	je fix2
cntfix:
	mov byte ptr BufOut[DI], AL
	inc SI
	dec DI
loop metka3
	pop CX
	mov DX, offset BufOut
	mov AH, 40h
	mov BX, Handler2
	int 21h
	printCRLF
	mov BufOut[SI], '$'
	mov dx, offset BufOut
	mov ah, 09h
	int 21h
	mov AX, 4C00h
	int 21h
;==============================================
print_hex	proc	near
	and	DL,0Fh
	add	DL,30h
	cmp	DL,3Ah
	jl	$print
	add	DL,07h
$print:	
	int	21H
   ret	
print_hex	endp	
;==============================================
print_reg_AX	proc	near
push	AX
push	BX
push	CX
push	DX
;==============================================
mov		BX,	AX
mov 	AH,02
    mov     DL,BH
	rcr	DL,4
	call 	print_hex
    mov DL,BH
	call	print_hex
;
	mov 	DL,BL
	rcr	DL,4
	call 	print_hex
	mov	DL,BL
	call	print_hex
;
pop		DX
pop		CX
pop		BX
pop		AX
print_letter CR
print_letter LF
ret
print_reg_AX	endp
;==============================================
print_reg_BX	proc near
push	AX
mov		AX,		BX
call	print_reg_AX
pop		AX
ret
print_reg_BX	endp

print_reg_СX	proc near
push	AX
mov		AX,		CX
call	print_reg_AX
pop		AX
ret
print_reg_СX	endp
;==============================================
print_reg_DX	proc near
push	AX
mov		AX,		DX
call	print_reg_AX
pop		AX
ret
print_reg_DX	endp
;==============================================
FileName	DB	16,0,16 dup (0)
Handler1 DW ?
Handler2 DW ?
BufIn 	DB 2048 dup ('L')
BufOut Db 2048 dup (0), '$'
	code_seg ends
         end start
