#================================== COMPILER ==================================#
CC = gcc
CPP = g++
#==============================================================================#

#==================================== FLAGS ===================================#
CFLAGS = -Wall -Wextra -Werror -g --std=c11
CPPFLAGS = -Wall -Wextra -Werror -g --std=c++17
UNAME := $(shell uname)
ifeq ($(UNAME), Linux)
	GFLAGS = -lm -lgtest -lsubunit
endif
ifeq ($(UNAME), Darwin)
	GFLAGS =  -I/opt/homebrew/include -L/opt/homebrew/lib -lgtest
endif
#==============================================================================#

#================================ TARGET NAMES ================================#
CLANG_FILE = ../materials/linters/.clang-format
TEST = test_laucnher
#==============================================================================#

#=============================== DIRECTORY NAMES ==============================#
BUILD_DIR = build
PARSER_DIR = parser
CONTROLLER_DIR = controller
FIGURE_DIR = figure
TEST_DIR = tests
DVI_DIR = docs
DIST_DIR = distribution
QTMAIN = main_window
QTGRAPHICS = graphics
#==============================================================================#

#====================== LIST OF FILE AND DIRS IN MODULES ======================#
SOURCE_TEST = $(wildcard $(TEST_DIR)/*.cpp)
#==============================================================================#

#================================= MAIN TARGETS ===============================#
.PHONY: all install uninstall cli desktop launch_cli test gcov_report clang_i clang_n cppcheck valgrind_cli valgrind_test rebuild clean dvi dist

all: clean install
ifeq ($(UNAME), Linux)
	./$(BUILD_DIR)/s21_3dViewer
endif
ifeq ($(UNAME), Darwin)
	open $(BUILD_DIR)/s21_3dViewer.app
endif

install: uninstall $(BUILD_DIR)
	@echo "installing process started..."
	@qmake s21_3dViewer.pro -o $(BUILD_DIR)
	@make -C $(BUILD_DIR)
	@rm -rf $(BUILD_DIR)/*o $(BUILD_DIR)/*h $(BUILD_DIR)/*cpp $(BUILD_DIR)/Makefile
	@echo "successfuly installed"

uninstall:
	@rm -rf $(BUILD_DIR)
	@echo "successfuly uninstalled"

tests: clean
	$(CPP) $(CPPFLAGS) $(SOURCE_TEST) -o $(TEST) $(GFLAGS)
	@echo "\nrunning tests..."
	@./$(TEST)
	@echo "\ndeleting test launcher..."
	@rm -rf $(TEST)

gcov_report: clean
	$(CPP) $(CPPFLAGS) $(SOURCE_TEST) -o $(TEST) $(GFLAGS) --coverage
	@./$(TEST)
	@lcov -t "tests" -o tests.info -c -d . -rc lcov_branch_coverage=1 \
								--exclude "$(PWD)/tests/*" \
								--exclude "/usr/*" \
								--exclude "/11/*"
	@genhtml -o report tests.info -rc lcov_branch_coverage=1
	@rm -rf *.gcno *.gcda *.gcov

format:
	@cp $(CLANG_FILE) .
	@find . -name '*.cpp' -o -name '*.hpp' -o -name '*.tpp' -o -name '*.h' 2>/dev/null | xargs -r clang-format -i
	@rm .clang-format
	@echo "formatting..."

format_check:
	@cp $(CLANG_FILE) .
	@find . -name '*.cpp' -o -name '*.hpp' -o -name '*.tpp' -o -name '*.h' 2>/dev/null | xargs -r clang-format -n
	@rm .clang-format
	@echo "checking format..."

cppcheck:
	find . -path './QtGifImage' -prune -o \( -name '*.cpp' -o -name '*.hpp' -o -name '*.tpp' -o -name '*.h' \) -print | xargs cppcheck --enable=all --suppress=missingIncludeSystem

valgrind_tests: clean
	$(CPP) $(CPPFLAGS) $(SOURCE_TEST) -o $(TEST) $(GFLAGS)
	@echo "\nstarting valgrind on tests...\n"
	valgrind -s -q --tool=memcheck --leak-check=yes --track-origins=yes --show-leak-kinds=all --verbose --log-file=valgrind_out.txt ./$(TEST)
	@cat valgrind_out.txt
	@rm valgrind_out.txt
	@rm -rf $(TEST)

rebuild: uninstall install

clean:
	@rm -rf *.gcno *.gcda *.info report $(TEST) *.dSYM dist a.out $(BUILD_DIR) $(DVI_DIR) $(DIST_DIR)
	@echo "cleaning...\n"

dvi:
	@rm -rf $(DVI_DIR)
	doxygen Doxyfile
ifeq ($(UNAME), Linux)
	xdg-open $(DVI_DIR)/html/index.html
endif
ifeq ($(UNAME), Darwin)
	open $(DVI_DIR)/html/index.html
endif

dist: clean $(DIST_DIR)
	@echo "Creating a distribution..."
	@echo "Distribution packaging..."
	@tar --exclude='./tests' --exclude='./$(DIST_DIR)' --exclude='./build' -czf $(DIST_DIR)/3dviewer_v1.0.tar.gz -C ./ .
	@echo "Distribution created"

#==============================================================================#

#============================== CREATE DIRECTORIES ============================#
$(BUILD_DIR):
	mkdir -p $(BUILD_DIR)
$(DIST_DIR):
	mkdir -p $(DIST_DIR)
#==============================================================================#