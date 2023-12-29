LIBTOOL=libtool
STATIC=-static
DYNAMIC=-dynamic
LIB_ARGS=$(STATIC) -L/usr/local/opt/zlib/lib -lz
LIB_SYNCTEX_A=libsynctex.a
CLI_SYNCTEX=synctex
LIB_SRC=synctex_parser.c synctex_parser_utils.c
MY_CFLAGS=-v -I. -D__SYNCTEX_WORK__


OBJS=$(LIB_SRC:.c=.o)

$(LIB_SYNCTEX_A): $(OBJS)
	$(LIBTOOL) $(LIB_ARGS) -o $@ $(OBJS)

%.o: %.c
	$(CC) $(MY_CFLAGS) -c -o $@ $<

$(CLI_SYNCTEX): synctex_main.c $(LIB_SYNCTEX_A)
	$(CC) $(MY_CFLAGS) -o$@ synctex_main.c -L. -lsynctex

clean:
	$(RM) $(LIB_SYNCTEX_A) $(OBJS)
