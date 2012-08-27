ACLOCAL_AMFLAGS = -I m4 --force
AUTOMAKE_OPTIONS = foreign
bin_PROGRAMS = memcachetest

memcachetest_SOURCES = \
                       boxmuller.c boxmuller.h \
                       libmemc.c libmemc.h \
                       main.c \
                       memcachetest.h \
                       metrics.c metrics.h \
                       timer.c \
                       vbucket.c vbucket.h

memcachetest_LDADD =
# Unfortunately I can't pass this variable through any of the AM_ flags..
if ENABLE_GCOV
memcachetest_LDADD += -lgcov
endif

reformat:
	astyle --mode=c \
               --quiet \
               --style=1tbs \
               --indent=spaces=4 \
               --indent-namespaces \
               --indent-col1-comments \
               --max-instatement-indent=78 \
               --pad-oper \
               --pad-header \
               --add-brackets \
               --unpad-paren \
               --align-pointer=name \
               --align-reference=name \
               $(top_srcdir)/libmemc.[ch] \
               $(top_srcdir)/main.c \
               $(top_srcdir)/memcachetest.h \
               $(top_srcdir)/metrics.[ch] \
               $(top_srcdir)/timer.c \
               $(top_srcdir)/vbucket.[ch]
