---
layout: post
title: "Credential Provider 자동 로그인(AutoLogon) 구현"
author: lynn.baek
date: 2018-12-17 11:22
tags: [Windows, Credential Provider]
comments: true
---



MS에서 Windows 로그인 화면을 사용자가 Customizing 할 수 있는 Credential Provider라는 dll을 제공하고 있습니다.

저는 [V2 Credential Provider Sample](https://code.msdn.microsoft.com/windowsapps/V2-Credential-Provider-7549a730)을 사용하였고, 이는 MS에서 제공하는 Credential Provider의 샘플입니다.

V2 Credential Provider Sample은 Windows8 이상의 OS부터 사용가능하고, 이전 버전의 Windows에서 사용할 Credential Provider를 구현하시려면, V2가 붙지 않은 Credential Provider Sample을 사용하시면 됩니다.

AutoLogon 부분 관련해서는 [Windows Dev Center에 올라와 있는 글](https://social.msdn.microsoft.com/Forums/windowsdesktop/en-US/496a9c88-4a5a-4e62-83b1-0035d1166297/does-credential-provider-have-auto-logon-possibility?forum=windowssecurity)을 보고 도움을 많이 받았습니다.



#### <u>자동 로그인(AutoLogon) 테스트는 꼭 VM환경에서 해주세요!</u>

잘못하다가 영영 로그인 화면에서 넘어갈 수 없을지도 모릅니다....ㅋㅋ



## CSampleCredential.cpp 수정
---

1. pbAutoLogon 플래그를 TRUE로 변경해준다.

```c++
HRESULT CSampleCredential::SetSelected(_Out_ BOOL *pbAutoLogon)
{
    *pbAutoLogon = TRUE; //FALSE를 TRUE로 바꿔준다.
    return S_OK;
}
```

2. GetSerialization은 submit 버튼이 눌리고 호출되는 함수이다. 여기서 submit 버튼이 눌리고 나서의 처리를 해주면 된다. user계정에 패스워드가 존재한다면, 패스워드를 ProtectIfNecessaryAndCopyPassword 함수에서 첫 번째 파라미터로 해당 user계정의 올바른 패스워드 들어가야만 자동 로그인에 성공한다. 

```c++
HRESULT CSampleCredential::GetSerialization(_Out_ CREDENTIAL_PROVIDER_GET_SERIALIZATION_RESPONSE *pcpgsr,
                                            _Out_ CREDENTIAL_PROVIDER_CREDENTIAL_SERIALIZATION *pcpcs,
                                            _Outptr_result_maybenull_ PWSTR *ppwszOptionalStatusText,
                                            _Out_ CREDENTIAL_PROVIDER_STATUS_ICON *pcpsiOptionalStatusIcon)
{
    HRESULT hr = E_UNEXPECTED;
    *pcpgsr = CPGSR_NO_CREDENTIAL_NOT_FINISHED;
    *ppwszOptionalStatusText = nullptr;
    *pcpsiOptionalStatusIcon = CPSI_NONE;
    ZeroMemory(pcpcs, sizeof(*pcpcs));

    // For local user, the domain and user name can be split from _pszQualifiedUserName (domain\username).
    // CredPackAuthenticationBuffer() cannot be used because it won't work with unlock scenario.
    if (_fIsLocalUser)
    {
        PWSTR pwzProtectedPassword;
        hr = ProtectIfNecessaryAndCopyPassword(_rgFieldStrings[SFI_PASSWORD], _cpus, &pwzProtectedPassword); //첫 번째 필드에서 패스워드를 바꿔주면 된다.(패스워드가 있을 때) 패스워드를 어떻게 가져올지는 알아서....
        if (SUCCEEDED(hr))
        {
            PWSTR pszDomain;
            PWSTR pszUsername;
            hr = SplitDomainAndUsername(_pszQualifiedUserName, &pszDomain, &pszUsername);
            if (SUCCEEDED(hr))
            {
                KERB_INTERACTIVE_UNLOCK_LOGON kiul;
                hr = KerbInteractiveUnlockLogonInit(pszDomain, pszUsername, pwzProtectedPassword, _cpus, &kiul);
                if (SUCCEEDED(hr))
                {
                    // We use KERB_INTERACTIVE_UNLOCK_LOGON in both unlock and logon scenarios.  It contains a
                    // KERB_INTERACTIVE_LOGON to hold the creds plus a LUID that is filled in for us by Winlogon
                    // as necessary.
                    hr = KerbInteractiveUnlockLogonPack(kiul, &pcpcs->rgbSerialization, &pcpcs->cbSerialization);
                    if (SUCCEEDED(hr))
                    {
                        ULONG ulAuthPackage;
                        hr = RetrieveNegotiateAuthPackage(&ulAuthPackage);
                        if (SUCCEEDED(hr))
                        {
                            pcpcs->ulAuthenticationPackage = ulAuthPackage;
                            pcpcs->clsidCredentialProvider = CLSID_CSample;
                            // At this point the credential has created the serialized credential used for logon
                            // By setting this to CPGSR_RETURN_CREDENTIAL_FINISHED we are letting logonUI know
                            // that we have all the information we need and it should attempt to submit the
                            // serialized credential.
                            *pcpgsr = CPGSR_RETURN_CREDENTIAL_FINISHED;
                        }
                    }
                }
                CoTaskMemFree(pszDomain);
                CoTaskMemFree(pszUsername);
            }
            CoTaskMemFree(pwzProtectedPassword);
        }
    }
    else
    {
        DWORD dwAuthFlags = CRED_PACK_PROTECTED_CREDENTIALS | CRED_PACK_ID_PROVIDER_CREDENTIALS;

        // First get the size of the authentication buffer to allocate
        if (!CredPackAuthenticationBuffer(dwAuthFlags, _pszQualifiedUserName, const_cast<PWSTR>(_rgFieldStrings[SFI_PASSWORD]), nullptr, &pcpcs->cbSerialization) &&
            (GetLastError() == ERROR_INSUFFICIENT_BUFFER))
        {
            pcpcs->rgbSerialization = static_cast<byte *>(CoTaskMemAlloc(pcpcs->cbSerialization));
            if (pcpcs->rgbSerialization != nullptr)
            {
                hr = S_OK;

                // Retrieve the authentication buffer
                if (CredPackAuthenticationBuffer(dwAuthFlags, _pszQualifiedUserName, const_cast<PWSTR>(_rgFieldStrings[SFI_PASSWORD]), pcpcs->rgbSerialization, &pcpcs->cbSerialization))
                {
                    ULONG ulAuthPackage;
                    hr = RetrieveNegotiateAuthPackage(&ulAuthPackage);
                    if (SUCCEEDED(hr))
                    {
                        pcpcs->ulAuthenticationPackage = ulAuthPackage;
                        pcpcs->clsidCredentialProvider = CLSID_CSample;

                        // At this point the credential has created the serialized credential used for logon
                        // By setting this to CPGSR_RETURN_CREDENTIAL_FINISHED we are letting logonUI know
                        // that we have all the information we need and it should attempt to submit the
                        // serialized credential.
                        *pcpgsr = CPGSR_RETURN_CREDENTIAL_FINISHED;
                    }
                }
                else
                {
                    hr = HRESULT_FROM_WIN32(GetLastError());
                    if (SUCCEEDED(hr))
                    {
                        hr = E_FAIL;
                    }
                }

                if (FAILED(hr))
                {
                    CoTaskMemFree(pcpcs->rgbSerialization);
                }
            }
            else
            {
                hr = E_OUTOFMEMORY;
            }
        }
    }
    return hr;
}
```



예를 들어 나의 user계정의 비밀번호가 `qwer1234`라면,  ProtectIfNecessaryAndCopyPassword 함수의 첫 번째 파라미터를 다음과 같이 변경해봅니다.

```c++
hr = ProtectIfNecessaryAndCopyPassword("qwer1234", _cpus, &pwzProtectedPassword);
```

## 결과 화면
---

그럼 다음과 같이 `패스워드 입력 필드`가 사라지고 `로그인`버튼만 있는 화면이 나타나게 됩니다.

## 변경 전
![변경 전 window 로그인 화면](/files/window_login1.PNG)

## 변경 후
![변경 후 window 로그인 화면](/files/window_login2.PNG)



