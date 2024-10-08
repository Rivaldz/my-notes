This For save session request, using after do filter move url filter still use last data 

        $dataRequest = session('data_request');
        $dataPasien = $this->casemixService->pasienData($dataRequest);
        $dataFilter = $this->casemixService->queryFilter();

        if (!$request->slug) {
            session()->forget('data_request');
            $dataPasien = null;
        }
