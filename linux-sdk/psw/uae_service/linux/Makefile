#
# Copyright (C) 2011-2016 Intel Corporation. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
#   * Redistributions of source code must retain the above copyright
#     notice, this list of conditions and the following disclaimer.
#   * Redistributions in binary form must reproduce the above copyright
#     notice, this list of conditions and the following disclaimer in
#     the documentation and/or other materials provided with the
#     distribution.
#   * Neither the name of Intel Corporation nor the names of its
#     contributors may be used to endorse or promote products derived
#     from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
# A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
# OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
# SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
# LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
# DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
# THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
#
#

TOP_DIR  = ../../..
include $(TOP_DIR)/buildenv.mk

IPC_COMMON_DIR        := $(TOP_DIR)/psw/ae/common
IPC_COMMON_SRC_DIR    := $(IPC_COMMON_DIR)/src
IPC_COMMON_INC_DIR    := $(IPC_COMMON_DIR)/inc
IPC_COMMON_PROTO_DIR  := $(IPC_COMMON_DIR)/proto
UAE_WRAPPER_DIR        := ../uae_wrapper
UAE_SRC_DIR    := $(UAE_WRAPPER_DIR)/src
UAE_INC_DIR    := $(UAE_WRAPPER_DIR)/inc
AE_COMMON_DIR    := $(LINUX_PSW_DIR)/ae/common 

INCLUDE += -I.
INCLUDE += -I$(COMMON_DIR)              \
           -I$(COMMON_DIR)/inc          \
           -I$(COMMON_DIR)/inc/internal \

INCLUDE += -I$(LINUX_PSW_DIR)/ae/common \
           -I$(LINUX_PSW_DIR)/ae/inc    \
           -I$(LINUX_PSW_DIR)/ae/inc/internal    \
           -I$(LINUX_SDK_DIR)/tseal

INCLUDE += -I$(LINUX_EXTERNAL_DIR)/epid/inc \
           -I$(IPC_COMMON_INC_DIR) \
           -I$(UAE_INC_DIR) \
           -I$(IPC_COMMON_PROTO_DIR) \
           -I$(LINUX_PSW_DIR)/ae/aesm_service/source

CXXFLAGS += -fPIC -Werror
PROTPBUF_CXXFLAGS := -fstack-protector -fPIC -Wall -Werror
ifdef DEBUG
	PROTPBUF_CXXFLAGS += -ggdb -DDEBUG -UNDEBUG
else
	PROTPBUF_CXXFLAGS += -O2 -UDEBUG -DNDEBUG
endif
ifeq ($(ARCH), x86)
	PROTPBUF_CXXFLAGS += -m32
else
	PROTPBUF_CXXFLAGS += -m64
endif

EXTERNAL_LIB += -lprotobuf 
 
vpath %.cpp .. $(COMMON_DIR)/src  $(IPC_COMMON_SRC_DIR) $(IPC_COMMON_PROTO_DIR) $(UAE_SRC_DIR) $(COMMON_DIR)/src $(AE_COMMON_DIR)



IPC_SRC  := AECloseSessionRequest.cpp    \
             AEGetQuoteResponse.cpp \
             AECloseSessionResponse.cpp  \
             AEInitQuoteRequest.cpp \
             AECreateSessionRequest.cpp  \
             AEInitQuoteResponse.cpp \
             AECreateSessionResponse.cpp  \
             AEInvokeServiceRequest.cpp \
             AEExchangeReportRequest.cpp   \
             AEInvokeServiceResponse.cpp \
             AEExchangeReportResponse.cpp \
             AEGetPsCapRequest.cpp \
             AEGetPsCapResponse.cpp \
             AEReportAttestationRequest.cpp \
             AEReportAttestationResponse.cpp \
             ProtobufSerializer.cpp \
             AEGetLaunchTokenRequest.cpp  \
             SocketTransporter.cpp \
             AEGetLaunchTokenResponse.cpp  \
             UnixCommunicationSocket.cpp \
             AEGetQuoteRequest.cpp \
             UnixSocketFactory.cpp \
             NonBlockingUnixCommunicationSocket.cpp \
             NonBlockingUnixSocketFactory.cpp

PROTOBUF_SRC  :=  messages.pb.cc


SRC :=       AEInternalServicesProvider.cpp \
             AEServicesImpl.cpp \
             AEServicesProvider.cpp \
             tae_ocall_api.cpp \
             uae_api.cpp \
             se_sig_rl.cpp \
             sgx_uae_service.cpp \
             uae_service_version.cpp \
             uae_service_assert.cpp

OBJ := $(SRC:.cpp=.o) $(IPC_SRC:.cpp=.o)  $(PROTOBUF_SRC:.cc=.o) 

LDUFLAGS:= -pthread -L$(BUILD_DIR)

DLLNAME  = libsgx_uae_service.so


.PHONY: all
all:  install_dll
 

.PHONY: install_dll
install_dll: $(DLLNAME)| $(BUILD_DIR)
	@$(CP) $^ $|

$(DLLNAME): $(OBJ)
	$(CXX) $(CXXFLAGS) $(OBJ) -shared $(LDUFLAGS) $(EXTERNAL_LIB) -o $@

%.o :%.cpp PROTPBUF
	$(CXX) $(CXXFLAGS) $(INCLUDE) -c $< -o $@

$(BUILD_DIR):
	@$(MKDIR) $@

.PHONY: PROTPBUF
PROTPBUF:
	$(MAKE) -C $(IPC_COMMON_PROTO_DIR) 
	(test -f messages.pb.o) || ($(CXX) $(PROTPBUF_CXXFLAGS) -c $(IPC_COMMON_PROTO_DIR)/messages.pb.cc -o messages.pb.o)

.PHONY: clean
clean:
	$(MAKE) -C $(IPC_COMMON_PROTO_DIR) clean
	@$(RM) $(OBJ)
	@$(RM) $(DLLNAME) $(BUILD_DIR)/$(DLLNAME) 

.PHONY: rebuild
rebuild: clean all
