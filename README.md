# JAVA_Jwt_Exception_Enum

### KeyCloak Error 처리를 위해서 만들었었는데 Error를 공통화 하지 않고 다르게 Front로 리턴하고 싶어서 반환 값을 찾아본 결과 String Message를 제외하고는 모두 같았다 그렇기 때문에 Enum을 생성해서  String Message을 분기처리하여 처리하도록 했다.

```java
package com.member.server.rabbit_market.util.exception;

import lombok.Getter;

import java.util.Arrays;
import java.util.List;

/**
 * jwt exception 분기처리 enum
 */
@Getter
public enum JwtExceptionEnum {

    INVALID_TOKEN("유효하지 않은 토큰" ,
            ErrorCode.INVALID_TOKEN.getValue() ,
            Arrays.asList("An error occurred while attempting to decode the Jwt: Signed JWT rejected" ,
                            "An error occurred while attempting to decode the Jwt: Malformed payload" ,
                            "An error occurred while attempting to decode the Jwt: Invalid" ,
                            "Bearer token is malformed")),
    EXPIRE_TOKEN("만료된 토큰" ,
            ErrorCode.EXPIRED_TOKEN.getValue() , List.of("An error occurred while attempting to decode the Jwt: Jwt expired")),
    NO_AUTHENTICATION("인증되지 않은 사용자" ,
            ErrorCode.UNAUTHORIZED.getValue() , List.of("Full authentication")),
    ACCESS_DENIED("권한이 없습니다." , ErrorCode.ACCESS_DENIED.getValue()
    , List.of("Access is denied") ),
    DEFAULT( "알 수 없는 오류" ,
            ErrorCode.UNAUTHORIZED.getValue(), List.of("IllegalStateException" , "null"));
    private final String ERROR;
    private final int code;
    private final List<String> ERROR_REASON;

    JwtExceptionEnum(String ERROR, int code, List<String> ERROR_REASON) {
        this.ERROR = ERROR;
        this.code = code;
        this.ERROR_REASON = ERROR_REASON;
    }

    public static JwtExceptionEnum findByErrorReason(String message){
        return Arrays.stream(JwtExceptionEnum.values())
                .filter(exceptionEnum -> hasMessage(exceptionEnum , message))
                .findAny()
                .orElse(JwtExceptionEnum.DEFAULT);
    }

    private static boolean hasMessage(JwtExceptionEnum exceptionEnum , String message){
        return exceptionEnum.ERROR_REASON.stream()
                .anyMatch(message::startsWith);
    }
}

```
