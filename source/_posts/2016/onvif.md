---
title: "onvif"
date: "2015-05-02 13:01"
categories：
    -erlang
tags:
    -onvif
---


#设备发现协议

发送如下XML消息。服务端会返回设备信息
```
<?xml version="1.0" encoding="utf-8"?>
<Envelope xmlns:dn="http://www.onvif.org/ver10/network/wsdl" xmlns="http://www.w3.org/2003/05/soap-envelope">
    <Header>
        <wsa:MessageID xmlns:wsa="http://schemas.xmlsoap.org/ws/2004/08/addressing">uuid:6d09a111-2662-49de-ba92-b429b35ec189</wsa:MessageID>
        <wsa:To xmlns:wsa="http://schemas.xmlsoap.org/ws/2004/08/addressing">urn:schemas-xmlsoap-org:ws:2005:04:discovery</wsa:To>
        <wsa:Action xmlns:wsa="http://schemas.xmlsoap.org/ws/2004/08/addressing">http://schemas.xmlsoap.org/ws/2005/04/discovery/Probe</wsa:Action>
    </Header>
    <Body>
        <Probe xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.xmlsoap.org/ws/2005/04/discovery">
            <Types>dn:NetworkVideoTransmitter</Types><Scopes />
        </Probe>
    </Body>
</Envelope>
```

##erlang 脚本

首先需要向239.255.255.250：3702 以UDP协议发送XML消息。
```
% udp Client 
client(Port,Msg) ->
  {ok, Socket} = gen_udp:open(0, [binary]),
  io:format("client opened socket=~p~n",[Socket]),
  ok = gen_udp:send(Socket, Port, 3702, Msg ),
  Value = udp_recieve(Socket,[]),

  gen_udp:close(Socket),
  Value.
% 
udp_recieve(Sock,Value)->
  receive
    {udp, Sock, _, _, Bin} ->
       %io:format("client received:~p~n",[Bin]),
      udp_recieve(Sock,[Bin]++Value)
  after 500 ->
    Value
  end.
 ```
 
 ##XML解析
 发送完消息后，接收服务端的消息，然后收到新的XML文件。使用erlang 的xmerl_scan 模块进行解析。解析完在编写脚本来获取需要的信息。
 ```
%% 从[xmlText] 中提取path 和 value 
%% Tuple: xmlText ,Res:[]
%% get path and value from xmlText
get_info([],Res) ->
  Res;
get_info([Tuple|R],Res)->
  case Tuple of
    #xmlText{parents = Parents,value = Value}
    ->  get_info(R,Res++[{Parents,Value}]);
    _Else->
      Res
  end.
%% 从xmlElement 根据节点名提取  子节点[xmlElement]
%%Tuple : xmlElement, Node : String
search(Tuple,Node) when is_tuple(Tuple) ->
  case Tuple of
    #xmlElement{nsinfo = {_, Node}} ->
      #xmlElement{content=Res}=Tuple,
      Res;
    #xmlElement{content= Children } ->
      search(Children,Node,[]);
    _Else ->
      []
  end.
search([],_Node,Res)-> Res;
search([Tuple|R],Node,Res) ->
  search(R,Node,Res ++ search(Tuple,Node)).
  ```
  ## 反馈消息格式
  ```
 <?xml version="1.0" encoding="UTF-8"?>
<env:Envelope xmlns:env="http://www.w3.org/2003/05/soap-envelope" xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:tt="http://www.onvif.org/ver10/schema" xmlns:tds="http://www.onvif.org/ver10/device/wsdl" xmlns:trt="http://www.onvif.org/ver10/media/wsdl" xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl" xmlns:tev="http://www.onvif.org/ver10/events/wsdl" xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl" xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl" xmlns:tst="http://www.onvif.org/ver10/storage/wsdl" xmlns:ter="http://www.onvif.org/ver10/error" xmlns:dn="http://www.onvif.org/ver10/network/wsdl" xmlns:tns1="http://www.onvif.org/ver10/topics" xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl" xmlns:wsdl="http://schemas.xmlsoap.org/wsdl" xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12" xmlns:http="http://schemas.xmlsoap.org/wsdl/http" xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery" xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing" xmlns:xop="http://www.w3.org/2004/08/xop/include" xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2" xmlns:wsa="http://www.w3.org/2005/08/addressing" xmlns:wstop="http://docs.oasis-open.org/wsn/t-1" xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2" xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2" xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2" xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl" xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2" xmlns:tnsn="http://www.eventextension.com/2011/event/topics"><env:Header><wsadis:MessageID>urn:uuid:ef7a28dc-1dd1-11b2-8199-8ce748e18e8a</wsadis:MessageID>
<wsadis:RelatesTo>uuid:7554cb93-bb81-4379-8eb2-c10f4bb259b1</wsadis:RelatesTo>
<wsadis:To>http://schemas.xmlsoap.org/ws/2004/08/addressing/role/anonymous</wsadis:To>
<wsadis:Action>http://schemas.xmlsoap.org/ws/2005/04/discovery/ProbeMatches</wsadis:Action>
<d:AppSequence InstanceId="1464710418" MessageNumber="1402"/>
</env:Header>
<env:Body><d:ProbeMatches><d:ProbeMatch><wsadis:EndpointReference><wsadis:Address>urn:uuid:ef7a28dc-1dd1-11b2-8199-8ce748e18e8a</wsadis:Address>
</wsadis:EndpointReference>
<d:Types>dn:NetworkVideoTransmitter tds:Device</d:Types>
<d:Scopes>onvif://www.onvif.org/type/video_encoder onvif://www.onvif.org/type/ptz onvif://www.onvif.org/type/audio_encoder onvif://www.onvif.org/location/ onvif://www.onvif.org/hardware/icamera8001 onvif://www.onvif.org/name/icamera8001 onvif://www.onvif.org/Profile/Streaming</d:Scopes>
<d:XAddrs>http://192.168.1.18/onvif/device_service</d:XAddrs>
<d:MetadataVersion>10</d:MetadataVersion>
</d:ProbeMatch>
</d:ProbeMatches>
</env:Body>
</env:Envelope>
<?xml version="1.0" encoding="UTF-8"?>
<env:Envelope xmlns:env="http://www.w3.org/2003/05/soap-envelope" xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:tt="http://www.onvif.org/ver10/schema" xmlns:tds="http://www.onvif.org/ver10/device/wsdl" xmlns:trt="http://www.onvif.org/ver10/media/wsdl" xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl" xmlns:tev="http://www.onvif.org/ver10/events/wsdl" xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl" xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl" xmlns:tst="http://www.onvif.org/ver10/storage/wsdl" xmlns:ter="http://www.onvif.org/ver10/error" xmlns:dn="http://www.onvif.org/ver10/network/wsdl" xmlns:tns1="http://www.onvif.org/ver10/topics" xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl" xmlns:wsdl="http://schemas.xmlsoap.org/wsdl" xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12" xmlns:http="http://schemas.xmlsoap.org/wsdl/http" xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery" xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing" xmlns:xop="http://www.w3.org/2004/08/xop/include" xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2" xmlns:wsa="http://www.w3.org/2005/08/addressing" xmlns:wstop="http://docs.oasis-open.org/wsn/t-1" xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2" xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2" xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2" xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl" xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2" xmlns:tnsn="http://www.eventextension.com/2011/event/topics"><env:Header><wsadis:MessageID>urn:uuid:ef7a28dc-1dd1-11b2-8199-8ce748e18e8a</wsadis:MessageID>
<wsadis:RelatesTo>uuid:6d09a111-2662-49de-ba92-b429b35ec189</wsadis:RelatesTo>
<wsadis:To>http://schemas.xmlsoap.org/ws/2004/08/addressing/role/anonymous</wsadis:To>
<wsadis:Action>http://schemas.xmlsoap.org/ws/2005/04/discovery/ProbeMatches</wsadis:Action>
<d:AppSequence InstanceId="1464710418" MessageNumber="1403"/>
</env:Header>
<env:Body><d:ProbeMatches><d:ProbeMatch><wsadis:EndpointReference><wsadis:Address>urn:uuid:ef7a28dc-1dd1-11b2-8199-8ce748e18e8a</wsadis:Address>
</wsadis:EndpointReference>
<d:Types>dn:NetworkVideoTransmitter tds:Device</d:Types>
<d:Scopes>onvif://www.onvif.org/type/video_encoder onvif://www.onvif.org/type/ptz onvif://www.onvif.org/type/audio_encoder onvif://www.onvif.org/location/ onvif://www.onvif.org/hardware/icamera8001 onvif://www.onvif.org/name/icamera8001 onvif://www.onvif.org/Profile/Streaming</d:Scopes>
<d:XAddrs>http://192.168.1.18/onvif/device_service</d:XAddrs>
<d:MetadataVersion>10</d:MetadataVersion>
</d:ProbeMatch>
</d:ProbeMatches>
</env:Body>
</env:Envelope>
```
##完整代码
```
%%%-------------------------------------------------------------------
%%% @author PC
%%% @copyright (C) 2016, <COMPANY>
%%% @doc
%%%
%%% @end
%%% Created : 30. 五月 2016 11:23
%%%-------------------------------------------------------------------
-module(xmlparser).
-author("PC").
-include_lib("xmerl/include/xmerl.hrl").
-include_lib("xmerl/include/xmerl_xpath.hrl").
-include_lib("xmerl/include/xmerl_xsd.hrl").
-include_lib("eunit/include/eunit.hrl").
-export([main/1,get_info/2,search/2]).


main(_v) ->
  {ok, Req } = file:read_file("c:/erl/soap-master/uri.xml"),
  Recv=client("239.255.255.250",Req),
  Fun=fun F([Xml|R])->
    io:format("get xml ~n"),
    {Doc,_} =  xmerl_scan:string(binary_to_list(Xml)),
    get_info(search(Doc,"XAddrs"),[])++F(R);
    F([])->[] end,
    io:format("~p~n",[Fun(Recv)]).

% Client code
client(Port,Msg) ->
  {ok, Socket} = gen_udp:open(0, [binary]),
  io:format("client opened socket=~p~n",[Socket]),
  ok = gen_udp:send(Socket, Port, 3702, Msg ),
  Value = udp_recieve(Socket,[]),

  gen_udp:close(Socket),
  Value.

udp_recieve(Sock,Value)->
  receive
    {udp, Sock, _, _, Bin} ->
       %io:format("client received:~p~n",[Bin]),
      udp_recieve(Sock,[Bin]++Value)
  after 500 ->
    Value
  end.

genera_path(Paths,NS)->% Paths: list() ,NS : namespace
  lists:flatmap(fun(Item)-> "/"++NS++":"++Item end,Paths).
%%get ns from xmlnode
get_env(Xml_ns,Url) -> %Xml_ns : xmlElement, Url: atom()
  List=element(3,Xml_ns),
  lists:filter(fun(Raw) -> case Raw  of
                             {_,Url } -> true;
                             _Else-> false end end, List).
%% Tuple: xmlText ,Res:[]
%% get path and value from xmlText
get_info([],Res) ->
  Res;
get_info([Tuple|R],Res)->
  case Tuple of
    #xmlText{parents = Parents,value = Value}
    ->  get_info(R,Res++[{Parents,Value}]);
    _Else->
      Res
  end.
%%Tuple : xmlElement, Node : String
search(Tuple,Node) when is_tuple(Tuple) ->
  case Tuple of
    #xmlElement{nsinfo = {_, Node}} ->
      #xmlElement{content=Res}=Tuple,
      Res;
    #xmlElement{content= Children } ->
      search(Children,Node,[]);
    _Else ->
      []
  end.
search([],_Node,Res)-> Res;
search([Tuple|R],Node,Res) ->
  search(R,Node,Res ++ search(Tuple,Node)).
 ```