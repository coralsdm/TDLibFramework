# TDLibFramework

[![CI](https://github.com/Swiftgram/TDLibFramework/actions/workflows/ci.yml/badge.svg)](https://github.com/Swiftgram/TDLibFramework/actions/workflows/ci.yml)

Project contains pre-compiled [TDLib](https://github.com/tdlib/td) binary for iOS, macOS, watchOS, tvOS and simulators in `.xcframework` bundle.

If you're looking for pure Swift library, check out [TDLibKit](https://github.com/Swiftgram/TDLibKit)

## Installation

### Xcode (SPM)

1. Install latest Xcode
2. Add `https://github.com/Swiftgram/TDLibFramework` as SPM dependency in `Project > Swift Packages`.
This could take a while cause it downloads ~300mb zip file with xcframework.
3. Add `TDLibFramework` as your target dependency.
4. Code!

### Cocoapods & Flutter

See the [docs](docs/COCOAPODS-and-FLUTTER.md)

## Usage

Simple examples can be found in [Tests](Tests) directory.

### Create client

```swift
let client: UnsafeMutableRawPointer! = td_json_client_create()
```

### Make request object

```swift
let request = ["@type": "getTextEntities", "text": "@telegram /test_command https://telegram.org telegram.me", "@extra": ["5", 7.0, "\\u00e4"]] as [String: Any]
```

### JSON Serialization and Deserialization

Small example for helper functions you will need to talk with TDLib

```swift
func dictToJSONString(_ dictionary: [String: Any]) -> String {
    let dictionaryData = try! JSONSerialization.data(withJSONObject: dictionary)
    return String(data: dictionaryData, encoding: .utf8)!
}

func JSONStringToDict(_ string: String) -> [String: Any] {
    let responseData = string.data(using: .utf8)!
    return try! JSONSerialization.jsonObject(with: responseData, options: .allowFragments) as! [String: Any]
}
```

### Synchronious requests

Only for methods with "[Can be called synchronously](https://github.com/tdlib/td/blob/73d8fb4b3584633b0ffde97a20bbff6602e7a5c4/td/generate/scheme/td_api.tl#L4294)" in docs

```swift
if let res = td_json_client_execute(client, dictToJSONString(request)) {
    let responseString = String(cString: res)
    let responseDict = JSONStringToDict(responseString)
    print("Response from TDLib \(responseDict)")
}
```

### Async requests

```swift
let request = [
    "@type": "setTdlibParameters",
    "api_hash": "5e6d7b36f0e363cf0c07baf2deb26076",
    "api_id": 287311,
    "application_version": "1.0",
    "database_directory": "tdlib",
    "database_encryption_key": nil,
    "device_model": "iOS",
    "enable_storage_optimizer": true,
    "files_directory": "",
    "ignore_file_names": true,
    "system_language_code": "en",
    "system_version": "Unknown",
    "use_chat_info_database": true,
    "use_file_database": true,
    "use_message_database": true,
    "use_secret_chats": true,
    "use_test_dc": false,
] as [String: Any]
// Send request
td_json_client_send(client, dictToJSONString(request))

// Block thread and wait for response (not more 5.0 seconds)
if let response = td_json_client_receive(client, 5.0) {
   let responseString = String(cString: res)
   let responseDict = JSONStringToDict(responseString)
   print("Async response from TDLib \(responseDict)")
}
```

Destroy client on exit

```swift
td_json_client_destroy(client)
```

## Releases

You can find latest releases at [Releases](https://github.com/Swiftgram/TDLibFramework/releases) page.

## Build

You can find more about build process in [Github Actions](.github/workflows/ci.yml) file.

## Credits

- Anton Glezman for [Build Guide](https://github.com/modestman/tdlib-swift) and basic implementation
- Telegram Team for [TDLib](https://github.com/tdlib/td)

## License

[MIT](LICENSE)
