# Spring MVC - XML/JSON

 스프링 MVC은  XML과 JSON 형식을 처리하는 방법을 제공하고 있다.

바로 @RequestBody , ResponseBody이다.

@RequestBody - 요청 몸체를 자바 객체로 변환할 때 사용 ex\)String. JSON

@ResponseBody - 자바 객체를 응답 몸체로 변환하기 위해 사용 ex\)JSON, XML

@RequestBody의 String 타입의 body 파라미터에 적용되어 있다. 이 경우 POST 방식으로 String 타입의 body파라미터가 전달된다.

