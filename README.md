# hllo_word
diyigegithub
#include<iostream>
  void CclientDlg::OnBnClickedButton1()
{
	// TODO: 在此添加控件通知处理程序代码
	HWND hipwnd = ::GetDlgItem(this->m_hWnd, IDC_EDIT_IP);
	char szip[MAX_PATH] = { 0 };
	::GetWindowText(hipwnd, szip, MAX_PATH);

	HWND hportwnd = ::GetDlgItem(this->m_hWnd, IDC_EDIT_PORT);
	char szport[MAX_PATH] = { 0 };
	::GetWindowText(hipwnd, szport, MAX_PATH);

	if (connection(szip, atoi(szport)))
	{
		Addtext("connect...", "[connect]");
	}
}
void CclientDlg::Addtext(char*lpsztext, char*lpszcaption)
{
	char sztext[MAX_PATH] = { 0 };
	HWND hwnd = ::GetDlgItem(this->m_hWnd, IDC_EDIT_TEXT);
	::GetWindowText(hwnd, sztext, MAX_PATH);
	::lstrcat(sztext, "\r\n");
	::lstrcat(sztext, lpszcaption);
	::lstrcat(sztext, lpsztext);
	::SetWindowText(hwnd, sztext);

}
bool CclientDlg::connection(char*lpszip, int lpport)
{
	SOCKET sock = socket(AF_INET, SOCK_STREAM, 0);
	if (INVALID_SOCKET == sock)
	{
		return FALSE;
	}
	sockaddr_in addr;
	addr.sin_family = AF_INET;
	addr.sin_port = htons(lpport);
	addr.sin_addr.S_un.S_addr = inet_addr(lpszip);
	if (0 != connect(sock, (sockaddr*)&addr, sizeof(addr)))
	{
		return FALSE;
	}
	m_sock = sock;
	::CreateThread(NULL, NULL, (LPTHREAD_START_ROUTINE)Threadproc, this, NULL, NULL);


	return TRUE;
}
UINT CclientDlg::Threadproc(LPVOID lpvoid)
{
	CclientDlg*lpthis = (CclientDlg*)lpvoid;
	lpthis->Acceptrecv();
	return 0;
}
void CclientDlg::Acceptrecv()
{
	
	
	char szbuf[MAX_PATH] = { 0 };
	while (TRUE)
	{
		int ired = recv(m_sock, szbuf, MAX_PATH, 0);
		if (0 >= ired)
		{
			continue;
		}
		Addtext(szbuf, "[recv]");
	}
}

void CclientDlg::OnBnClickedButton2()
{
	// TODO: 在此添加控件通知处理程序代码
	HWND hsendwnd = ::GetDlgItem(this->m_hWnd, IDC_EDIT_SEND);
	char szsend[MAX_PATH] = { 0 };
	::GetWindowText(hsendwnd, szsend, MAX_PATH);
	send(m_sock, szsend, (1 + ::lstrlen(szsend)), 0);
	Addtext(szsend, "[send]");
}
