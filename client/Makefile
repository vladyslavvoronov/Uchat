NAME		= client
SRC_DIR		= src/
OBJ_DIR		= obj/
INC_DIR		= inc/
DIRS		= $(notdir $(wildcard $(SRC_DIR)*))
FILES		= $(foreach dir, $(DIRS), $(basename $(wildcard \
			  $(SRC_DIR)$(dir)/*.c)))
SRC		= $(FILES:%=%.c)
OBJ		= $(SRC:src/%.c=$(OBJ_DIR)%.o)
INC_H		= $(wildcard $(INC_DIR)*.h)

LIB_DIR		= libs
LIB_LIST	= cjson
LIB_DIRS	= $(foreach libdirs, $(LIB_LIST), $(LIB_DIR)/$(libdirs)/)
LIB_BIN		= $(join $(LIB_DIRS), $(addsuffix .a, $(LIB_LIST)))
LIB_INC		= $(addsuffix $(INC_DIR), $(LIB_DIRS))

CC		= clang
GFLAGS		= -std=c11 -pipe -Wall -Wextra -Werror -Wpedantic -g -fsanitize=address \
	-Wno-unused-command-line-argument -Wno-unused-variable \
	-Wno-unused-function -Wno-unused-parameter -Wno-everything -g 
IFLAGS		= $(addprefix -I, $(LIB_INC) $(INC_DIR))
GTKFLAGS    	= `pkg-config --cflags gtk+-3.0` `pkg-config --libs gtk+-3.0`
COMPILE		= $(CC) $(GFLAGS) $(GTKFLAGS) $(IFLAGS) $(LIB_BIN)
MAKE_M		= make -sf Makefile -C
MKDIR		= mkdir -p
RM		= /bin/rm -rf

all: $(NAME)

$(NAME): $(LIB_LIST) $(OBJ_DIR) $(OBJ)
	@$(COMPILE) $(OBJ) -L/usr/local/opt/openssl/lib/ -lssl -lcrypto -lmx -rdynamic -o $(NAME)

$(LIB_BIN): $(LIB_LIST)

$(LIB_LIST): $(LIB_DIRS)
	@$(MAKE_M) $(LIB_DIR)/$@

$(OBJ_DIR):
	@$(MKDIR) $@ $(foreach dir, $(DIRS), $@/$(dir))

$(OBJ_DIR)%.o: $(SRC_DIR)%.c $(INC_H) $(LIB_BIN)
	@$(COMPILE) -o $@ -c $< -I/usr/local/opt/openssl/include/

clean:
	@$(MAKE_M) libs/cjson  $@
	@$(RM) $(OBJ_DIR)

uninstall:
	@$(MAKE_M) libs/cjson $@
	@$(RM) $(OBJ_DIR) $(NAME)

reinstall: uninstall all

.PHONY: all clean uninstall reinstall $(LIB_LIST)
