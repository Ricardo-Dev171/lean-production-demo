import React, { useMemo, useState } from 'react';

const initialOps = [
  {
    id: 'OP-25487',
    seq: 1,
    model: 'MOD-A01',
    qty: 50,
    plannedMin: 120,
    priority: 'Alta',
    status: 'Disponível',
    technician: 'João',
    creator: 'PCP',
    responsible: 'João',
    scheduledDate: '2026-03-18',
    startTime: null,
    pauses: [],
    approved: 0,
    rejected: 0,
    rejectionReason: '',
    finalNote: ''
  },
  {
    id: 'OP-25488',
    seq: 2,
    model: 'MOD-B14',
    qty: 30,
    plannedMin: 90,
    priority: 'Normal',
    status: 'Disponível',
    technician: 'Carlos',
    creator: 'PCP',
    responsible: 'Carlos',
    scheduledDate: '2026-03-18',
    startTime: null,
    pauses: [],
    approved: 0,
    rejected: 0,
    rejectionReason: '',
    finalNote: ''
  },
  {
    id: 'OP-25489',
    seq: 3,
    model: 'MOD-C07',
    qty: 80,
    plannedMin: 160,
    priority: 'Baixa',
    status: 'Reprogramada',
    technician: 'Ana',
    creator: 'PCP',
    responsible: 'Ana',
    scheduledDate: '2026-03-19',
    startTime: null,
    pauses: [],
    approved: 0,
    rejected: 0,
    rejectionReason: '',
    finalNote: ''
  }
];

const pauseReasons = [
  'Falta de material',
  'Aguardando liberação',
  'Problema de teste',
  'Prioridade alterada',
  'Ajuste de processo'
];

const rejectReasons = [
  'Falha funcional',
  'Defeito visual',
  'Erro de montagem',
  'Componente danificado',
  'Falha no teste'
];

const technicians = ['João', 'Carlos', 'Ana', 'Ricardo'];

function KPI({ label, value, suffix = '' }) {
  return (
    <div className="card stat-card">
      <div className="muted small">{label}</div>
      <div className="stat-value">{value}{suffix}</div>
    </div>
  );
}

function NavButton({ label, onClick }) {
  return (
    <button onClick={onClick} className="card nav-card">
      <div className="nav-title">{label}</div>
      <div className="muted small">Acessar módulo</div>
    </button>
  );
}

export default function App() {
  const [currentScreen, setCurrentScreen] = useState('login');
  const [user, setUser] = useState({ name: 'Ricardo', profile: 'Supervisor', technician: 'João' });
  const [ops, setOps] = useState(initialOps);
  const [selectedOpId, setSelectedOpId] = useState(null);
  const [loginEmail, setLoginEmail] = useState('ricardo@empresa.com');
  const [loginPassword, setLoginPassword] = useState('123456');
  const [pauseForm, setPauseForm] = useState({ reason: '', note: '' });
  const [finishForm, setFinishForm] = useState({ approved: '', rejected: '', rejectionReason: '', note: '' });
  const [dashboardPeriod, setDashboardPeriod] = useState('Hoje');
  const [filters, setFilters] = useState({ status: 'Todos', priority: 'Todas', technician: 'Todos' });

  const selectedOp = useMemo(() => ops.find((o) => o.id === selectedOpId) || null, [ops, selectedOpId]);
  const activeExec = useMemo(() => ops.find((o) => o.technician === user.technician && o.status === 'Em execução'), [ops, user.technician]);

  const statusClass = (status) => ({
    'Disponível': 'badge gray',
    'Planejada': 'badge gray',
    'Em execução': 'badge blue',
    'Pausada': 'badge amber',
    'Finalizada no prazo': 'badge green',
    'Finalizada fora do prazo': 'badge orange',
    'Reprogramada': 'badge violet',
    'Cancelada': 'badge red'
  }[status] || 'badge gray');

  const priorityClass = (priority) => ({
    Alta: 'badge red',
    Normal: 'badge blue',
    Baixa: 'badge gray'
  }[priority] || 'badge gray');

  const formatMin = (min) => {
    const h = Math.floor((min || 0) / 60);
    const m = (min || 0) % 60;
    return `${String(h).padStart(2, '0')}:${String(m).padStart(2, '0')}`;
  };

  const calcGross = (op) => {
    if (!op?.startTime) return 0;
    if (op.status === 'Em execução' || op.status === 'Pausada') return 72;
    if (op.status.startsWith('Finalizada')) return op.model === 'MOD-A01' ? 107 : 98;
    return 0;
  };

  const calcPause = (op) => op?.pauses?.reduce((acc, p) => acc + (p.minutes || 0), 0) || 0;
  const calcNet = (op) => Math.max(calcGross(op) - calcPause(op), 0);

  const kpis = useMemo(() => {
    const today = ops.filter((o) => o.scheduledDate === '2026-03-18');
    const totalRejected = today.reduce((acc, o) => acc + (o.rejected || 0), 0);
    const totalProcessed = today.reduce((acc, o) => acc + (o.approved || 0) + (o.rejected || 0), 0);
    return {
      planned: today.length,
      done: today.filter((o) => o.status.startsWith('Finalizada')).length,
      running: today.filter((o) => o.status === 'Em execução').length,
      pending: today.filter((o) => ['Disponível', 'Planejada', 'Pausada'].includes(o.status)).length,
      downtime: today.reduce((acc, o) => acc + calcPause(o), 0),
      rejectRate: totalProcessed ? ((totalRejected / totalProcessed) * 100).toFixed(1) : '0.0',
      onTime: today.filter((o) => o.status === 'Finalizada no prazo').length,
      late: today.filter((o) => o.status === 'Finalizada fora do prazo').length,
      reprogrammed: ops.filter((o) => o.status === 'Reprogramada').length
    };
  }, [ops]);

  const filteredOps = useMemo(() => {
    return ops.filter((o) => {
      const okStatus = filters.status === 'Todos' || o.status === filters.status;
      const okPriority = filters.priority === 'Todas' || o.priority === filters.priority;
      const okTech = filters.technician === 'Todos' || o.technician === filters.technician;
      return okStatus && okPriority && okTech;
    });
  }, [ops, filters]);

  const myOps = useMemo(() => ops.filter((o) => o.technician === user.technician), [ops, user.technician]);

  const handleLogin = () => {
    const profile = loginEmail.includes('consulta') ? 'Consulta' : 'Supervisor';
    setUser({ name: 'Ricardo', profile, technician: 'João' });
    setCurrentScreen('home');
  };

  const openOp = (opId, target = 'execution') => {
    setSelectedOpId(opId);
    setCurrentScreen(target);
  };

  const startOp = (opId) => {
    const op = ops.find((o) => o.id === opId);
    if (!op) return;
    if (activeExec && activeExec.id !== opId) {
      window.alert(`O técnico ${user.technician} já possui a OP ${activeExec.id} em execução.`);
      return;
    }
    setOps((prev) => prev.map((o) => (o.id === opId ? { ...o, status: 'Em execução', startTime: new Date().toISOString() } : o)));
    setSelectedOpId(opId);
    setCurrentScreen('execution');
  };

  const confirmPause = () => {
    if (!pauseForm.reason) {
      window.alert('Selecione o motivo da pausa.');
      return;
    }
    setOps((prev) => prev.map((o) => (
      o.id === selectedOpId
        ? { ...o, status: 'Pausada', pauses: [...o.pauses, { reason: pauseForm.reason, note: pauseForm.note, minutes: 12 }] }
        : o
    )));
    setPauseForm({ reason: '', note: '' });
    setCurrentScreen('paused');
  };

  const resumeOp = () => {
    setOps((prev) => prev.map((o) => (o.id === selectedOpId ? { ...o, status: 'Em execução' } : o)));
    setCurrentScreen('execution');
  };

  const finishOp = () => {
    const approved = Number(finishForm.approved || 0);
    const rejected = Number(finishForm.rejected || 0);
    if (approved + rejected <= 0) {
      window.alert('Informe as quantidades aprovadas ou reprovadas.');
      return;
    }
    if (rejected > 0 && !finishForm.rejectionReason) {
      window.alert('Selecione o motivo da reprovação.');
      return;
    }
    setOps((prev) => prev.map((o) => {
      if (o.id !== selectedOpId) return o;
      const net = Math.max((o.id === 'OP-25487' ? 107 : 98) - calcPause(o), 0);
      return {
        ...o,
        approved,
        rejected,
        rejectionReason: finishForm.rejectionReason,
        finalNote: finishForm.note,
        status: net <= o.plannedMin ? 'Finalizada no prazo' : 'Finalizada fora do prazo'
      };
    }));
    setFinishForm({ approved: '', rejected: '', rejectionReason: '', note: '' });
    setCurrentScreen('home');
  };

  const reprogramOp = (opId) => {
    setOps((prev) => prev.map((o) => (o.id === opId ? { ...o, status: 'Reprogramada', scheduledDate: '2026-03-19' } : o)));
  };

  const cancelOp = (opId) => {
    setOps((prev) => prev.map((o) => (o.id === opId ? { ...o, status: 'Cancelada' } : o)));
  };

  const renderHeader = (title, subtitle) => (
    <div className="header-row">
      <div>
        <h1>{title}</h1>
        <p className="muted">{subtitle}</p>
      </div>
      <div className="header-pills">
        <span className="pill">{user.name}</span>
        <span className="pill">{user.profile}</span>
      </div>
    </div>
  );

  if (currentScreen === 'login') {
    return (
      <div className="login-page">
        <div className="login-card card">
          <div className="center">
            <div className="logo-title">Production Flow Control</div>
            <div className="muted">Demo navegável pronta para Vercel</div>
          </div>
          <div className="field-group">
            <label>E-mail/Login</label>
            <input value={loginEmail} onChange={(e) => setLoginEmail(e.target.value)} />
          </div>
          <div className="field-group">
            <label>Senha</label>
            <input type="password" value={loginPassword} onChange={(e) => setLoginPassword(e.target.value)} />
          </div>
          <button onClick={handleLogin} className="btn primary block">Entrar</button>
          <div className="muted center small">Use qualquer login para navegar pela demo.</div>
        </div>
      </div>
    );
  }

  return (
    <div className="app-shell">
      <aside className="sidebar">
        <div>
          <div className="sidebar-title">Production Flow Control</div>
          <div className="sidebar-subtitle">Demo do app industrial</div>
        </div>
        <nav className="menu">
          {[
            ['Home', 'home'],
            ['Minhas OPs', 'myops'],
            ['Execução', 'execution'],
            ['Painel do Dia', 'daypanel'],
            ['Dashboard', 'dashboard'],
            ['Supervisão', 'supervision']
          ].map(([label, screen]) => (
            <button key={screen} onClick={() => setCurrentScreen(screen)} className={`menu-btn ${currentScreen === screen ? 'active' : ''}`}>
              {label}
            </button>
          ))}
        </nav>
        <div className="sidebar-footer">
          <div className="footer-card">
            <div className="footer-title">Usuário logado</div>
            <div>{user.name}</div>
            <div>Perfil: {user.profile}</div>
          </div>
          <button onClick={() => setCurrentScreen('login')} className="btn secondary block">Sair</button>
        </div>
      </aside>

      <main className="content">
        {currentScreen === 'home' && (
          <div>
            {renderHeader('Home', 'Visão executiva da operação diária')}
            <div className="grid cols-6 gap">
              <KPI label="OPs programadas" value={kpis.planned} />
              <KPI label="Concluídas" value={kpis.done} />
              <KPI label="Em execução" value={kpis.running} />
              <KPI label="Pendentes" value={kpis.pending} />
              <KPI label="Tempo parado" value={kpis.downtime} suffix=" min" />
              <KPI label="Taxa de refugo" value={kpis.rejectRate} suffix="%" />
            </div>
            <div className="grid cols-3 gap top-gap">
              <NavButton label="Minhas OPs" onClick={() => setCurrentScreen('myops')} />
              <NavButton label="Painel do Dia" onClick={() => setCurrentScreen('daypanel')} />
              <NavButton label="Dashboard" onClick={() => setCurrentScreen('dashboard')} />
              <NavButton label="Supervisão" onClick={() => setCurrentScreen('supervision')} />
              <NavButton label="Execução em andamento" onClick={() => setCurrentScreen(selectedOp ? 'execution' : 'myops')} />
              <div className="card nav-card static-card">
                <div className="nav-title">Destaque do dia</div>
                <div className="small-line">OPs no prazo: <strong>{kpis.onTime}</strong></div>
                <div className="small-line">Fora do prazo: <strong>{kpis.late}</strong></div>
                <div className="small-line">Reprogramadas: <strong>{kpis.reprogrammed}</strong></div>
              </div>
            </div>
            <div className="card top-gap">
              <div className="section-title">Resumo rápido das OPs</div>
              <div className="section-subtitle">Acompanhamento consolidado do dia</div>
              <div className="table-wrap top-gap-sm">
                <table>
                  <thead>
                    <tr>
                      <th>OP</th>
                      <th>Modelo</th>
                      <th>Técnico</th>
                      <th>Qtd</th>
                      <th>Previsto</th>
                      <th>Status</th>
                    </tr>
                  </thead>
                  <tbody>
                    {ops.map((op) => (
                      <tr key={op.id}>
                        <td><strong>{op.id}</strong></td>
                        <td>{op.model}</td>
                        <td>{op.technician}</td>
                        <td>{op.qty}</td>
                        <td>{op.plannedMin} min</td>
                        <td><span className={statusClass(op.status)}>{op.status}</span></td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        )}

        {currentScreen === 'myops' && (
          <div>
            {renderHeader('Minhas OPs', `Técnico vinculado: ${user.technician}`)}
            <div className="filters-row">
              <select value={filters.status} onChange={(e) => setFilters((f) => ({ ...f, status: e.target.value }))}>
                <option>Todos</option>
                <option>Disponível</option>
                <option>Em execução</option>
                <option>Pausada</option>
                <option>Finalizada no prazo</option>
                <option>Finalizada fora do prazo</option>
                <option>Reprogramada</option>
              </select>
              <select value={filters.priority} onChange={(e) => setFilters((f) => ({ ...f, priority: e.target.value }))}>
                <option>Todas</option>
                <option>Alta</option>
                <option>Normal</option>
                <option>Baixa</option>
              </select>
              <button onClick={() => setFilters({ status: 'Todos', priority: 'Todas', technician: 'Todos' })} className="btn secondary">Limpar filtros</button>
            </div>
            <div className="card table-card">
              <div className="table-wrap">
                <table>
                  <thead>
                    <tr>
                      <th>OP</th>
                      <th>Seq</th>
                      <th>Modelo</th>
                      <th>Qtd</th>
                      <th>Tempo Previsto</th>
                      <th>Prioridade</th>
                      <th>Status</th>
                      <th>Ações</th>
                    </tr>
                  </thead>
                  <tbody>
                    {myOps.filter((op) => {
                      const okStatus = filters.status === 'Todos' || op.status === filters.status;
                      const okPriority = filters.priority === 'Todas' || op.priority === filters.priority;
                      return okStatus && okPriority;
                    }).map((op) => (
                      <tr key={op.id}>
                        <td><strong>{op.id}</strong></td>
                        <td>{op.seq}</td>
                        <td>{op.model}</td>
                        <td>{op.qty}</td>
                        <td>{op.plannedMin} min</td>
                        <td><span className={priorityClass(op.priority)}>{op.priority}</span></td>
                        <td><span className={statusClass(op.status)}>{op.status}</span></td>
                        <td>
                          <div className="action-row">
                            <button onClick={() => startOp(op.id)} className="btn primary small-btn">Iniciar</button>
                            <button onClick={() => openOp(op.id, op.status === 'Pausada' ? 'paused' : 'execution')} className="btn secondary small-btn">Detalhes</button>
                          </div>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        )}

        {currentScreen === 'execution' && (
          <div>
            {renderHeader('OP em execução', 'Tela principal do técnico')}
            {!selectedOp ? (
              <div className="card muted">Selecione uma OP em Minhas OPs.</div>
            ) : (
              <div className="stack-gap">
                <div className="card">
                  <div className="execution-grid">
                    <div>
                      <div className="execution-title-row">
                        <div className="execution-title">{selectedOp.id}</div>
                        <span className={statusClass(selectedOp.status)}>{selectedOp.status}</span>
                      </div>
                      <div className="info-grid">
                        <div><span className="muted">Modelo:</span> <strong>{selectedOp.model}</strong></div>
                        <div><span className="muted">Técnico:</span> <strong>{selectedOp.technician}</strong></div>
                        <div><span className="muted">Quantidade:</span> <strong>{selectedOp.qty}</strong></div>
                        <div><span className="muted">Tempo previsto:</span> <strong>{selectedOp.plannedMin} min</strong></div>
                        <div><span className="muted">Sequência PCP:</span> <strong>{selectedOp.seq}</strong></div>
                        <div><span className="muted">Prioridade:</span> <strong>{selectedOp.priority}</strong></div>
                      </div>
                    </div>
                    <div className="metrics-grid">
                      <div className="metric-box">
                        <div className="muted small">Tempo bruto</div>
                        <div className="metric-value">{formatMin(calcGross(selectedOp))}</div>
                      </div>
                      <div className="metric-box">
                        <div className="muted small">Tempo parado</div>
                        <div className="metric-value">{formatMin(calcPause(selectedOp))}</div>
                      </div>
                      <div className="metric-box">
                        <div className="muted small">Tempo líquido</div>
                        <div className="metric-value">{formatMin(calcNet(selectedOp))}</div>
                      </div>
                    </div>
                  </div>
                </div>
                <div className="action-row">
                  <button onClick={() => setCurrentScreen('pausemodal')} className="btn warning">Pausar</button>
                  <button onClick={() => {
                    setFinishForm({ approved: String(selectedOp.qty - 2), rejected: '2', rejectionReason: 'Falha no teste', note: 'Lote com ajuste pontual.' });
                    setCurrentScreen('finish');
                  }} className="btn success">Finalizar</button>
                  <button onClick={() => setCurrentScreen('myops')} className="btn secondary">Voltar</button>
                </div>
                <div className="card">
                  <div className="section-title">Histórico de pausas</div>
                  {selectedOp.pauses.length === 0 ? (
                    <div className="muted top-gap-sm">Ainda não há pausas registradas.</div>
                  ) : (
                    <div className="stack-gap-sm top-gap-sm">
                      {selectedOp.pauses.map((pause, idx) => (
                        <div key={`${pause.reason}-${idx}`} className="pause-item">
                          <div><strong>{pause.reason}</strong></div>
                          <div className="muted">Tempo: {pause.minutes} min</div>
                          {pause.note ? <div className="muted">Obs.: {pause.note}</div> : null}
                        </div>
                      ))}
                    </div>
                  )}
                </div>
              </div>
            )}
          </div>
        )}

        {currentScreen === 'pausemodal' && selectedOp && (
          <div>
            {renderHeader('Registrar pausa', `OP ${selectedOp.id}`)}
            <div className="card narrow-card">
              <div className="field-group">
                <label>Motivo da pausa</label>
                <select value={pauseForm.reason} onChange={(e) => setPauseForm((f) => ({ ...f, reason: e.target.value }))}>
                  <option value="">Selecione</option>
                  {pauseReasons.map((r) => <option key={r}>{r}</option>)}
                </select>
              </div>
              <div className="field-group">
                <label>Observação</label>
                <textarea value={pauseForm.note} onChange={(e) => setPauseForm((f) => ({ ...f, note: e.target.value }))} rows="4" />
              </div>
              <div className="action-row">
                <button onClick={confirmPause} className="btn primary">Confirmar pausa</button>
                <button onClick={() => setCurrentScreen('execution')} className="btn secondary">Cancelar</button>
              </div>
            </div>
          </div>
        )}

        {currentScreen === 'paused' && selectedOp && (
          <div>
            {renderHeader('OP pausada', `OP ${selectedOp.id}`)}
            <div className="card">
              <div className="execution-title-row">
                <div className="execution-title">{selectedOp.id}</div>
                <span className={statusClass(selectedOp.status)}>{selectedOp.status}</span>
              </div>
              <div className="top-gap-sm"><span className="muted">Motivo atual:</span> <strong>{selectedOp.pauses[selectedOp.pauses.length - 1]?.reason}</strong></div>
              <div className="top-gap-sm"><span className="muted">Tempo pausado acumulado:</span> <strong>{calcPause(selectedOp)} min</strong></div>
              <button onClick={resumeOp} className="btn primary top-gap">Retomar OP</button>
            </div>
          </div>
        )}

        {currentScreen === 'finish' && selectedOp && (
          <div>
            {renderHeader('Finalizar OP', `Encerramento da ${selectedOp.id}`)}
            <div className="finish-grid">
              <div className="card">
                <div className="finish-form-grid">
                  <div className="field-group">
                    <label>Quantidade aprovada</label>
                    <input value={finishForm.approved} onChange={(e) => setFinishForm((f) => ({ ...f, approved: e.target.value }))} />
                  </div>
                  <div className="field-group">
                    <label>Quantidade reprovada</label>
                    <input value={finishForm.rejected} onChange={(e) => setFinishForm((f) => ({ ...f, rejected: e.target.value }))} />
                  </div>
                  <div className="field-group span-2">
                    <label>Motivo da reprovação</label>
                    <select value={finishForm.rejectionReason} onChange={(e) => setFinishForm((f) => ({ ...f, rejectionReason: e.target.value }))}>
                      <option value="">Selecione</option>
                      {rejectReasons.map((r) => <option key={r}>{r}</option>)}
                    </select>
                  </div>
                  <div className="field-group span-2">
                    <label>Observação final</label>
                    <textarea value={finishForm.note} onChange={(e) => setFinishForm((f) => ({ ...f, note: e.target.value }))} rows="4" />
                  </div>
                </div>
                <div className="action-row top-gap">
                  <button onClick={finishOp} className="btn success">Concluir OP</button>
                  <button onClick={() => setCurrentScreen('execution')} className="btn secondary">Voltar</button>
                </div>
              </div>
              <div className="card">
                <div className="section-title">Resumo automático</div>
                <div className="summary-list top-gap-sm">
                  <div className="summary-item"><span className="muted">Tempo bruto</span><strong>{calcGross(selectedOp)} min</strong></div>
                  <div className="summary-item"><span className="muted">Tempo parado</span><strong>{calcPause(selectedOp)} min</strong></div>
                  <div className="summary-item"><span className="muted">Tempo líquido</span><strong>{calcNet(selectedOp)} min</strong></div>
                  <div className="summary-item"><span className="muted">Tempo previsto</span><strong>{selectedOp.plannedMin} min</strong></div>
                  <div className="summary-item"><span className="muted">Situação</span><strong className={calcNet(selectedOp) <= selectedOp.plannedMin ? 'ok' : 'warn'}>{calcNet(selectedOp) <= selectedOp.plannedMin ? 'Dentro do prazo' : 'Fora do prazo'}</strong></div>
                </div>
              </div>
            </div>
          </div>
        )}

        {currentScreen === 'daypanel' && (
          <div>
            {renderHeader('Painel do dia', 'Gestão visual da programação e execução')}
            <div className="grid cols-7 gap">
              <KPI label="Programado" value={kpis.planned} />
              <KPI label="Concluído" value={kpis.done} />
              <KPI label="Em execução" value={kpis.running} />
              <KPI label="Pendente" value={kpis.pending} />
              <KPI label="No prazo" value={kpis.onTime} />
              <KPI label="Fora prazo" value={kpis.late} />
              <KPI label="Reprogramado" value={kpis.reprogrammed} />
            </div>
            <div className="panel-grid top-gap">
              <div className="card table-card">
                <div className="table-wrap">
                  <table>
                    <thead>
                      <tr>
                        <th>OP</th>
                        <th>Técnico</th>
                        <th>Modelo</th>
                        <th>Qtd</th>
                        <th>Status</th>
                        <th>Previsto</th>
                        <th>Realizado</th>
                        <th>Situação</th>
                      </tr>
                    </thead>
                    <tbody>
                      {ops.map((op) => (
                        <tr key={op.id}>
                          <td><strong>{op.id}</strong></td>
                          <td>{op.technician}</td>
                          <td>{op.model}</td>
                          <td>{op.qty}</td>
                          <td><span className={statusClass(op.status)}>{op.status}</span></td>
                          <td>{op.plannedMin} min</td>
                          <td>{op.status.startsWith('Finalizada') || op.status === 'Em execução' || op.status === 'Pausada' ? `${calcNet(op)} min` : '-'}</td>
                          <td>{op.status.startsWith('Finalizada') ? (calcNet(op) <= op.plannedMin ? 'Dentro do prazo' : 'Fora do prazo') : op.status === 'Reprogramada' ? 'Pendente' : 'Em andamento'}</td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </div>
              </div>
              <div className="card">
                <div className="section-title">Planejado x realizado</div>
                <div className="stack-gap top-gap-sm">
                  {ops.map((op) => {
                    const percent = Math.min(Math.round((calcNet(op) / Math.max(op.plannedMin, 1)) * 100), 100);
                    return (
                      <div key={op.id}>
                        <div className="bar-label"><span>{op.id}</span><span>{calcNet(op) || 0}/{op.plannedMin} min</span></div>
                        <div className="bar-bg"><div className="bar-fill dark" style={{ width: `${percent}%` }} /></div>
                      </div>
                    );
                  })}
                </div>
              </div>
            </div>
          </div>
        )}

        {currentScreen === 'dashboard' && (
          <div>
            {renderHeader('Dashboard gerencial', 'Análise consolidada para tomada de decisão')}
            <div className="filters-row five">
              <select value={dashboardPeriod} onChange={(e) => setDashboardPeriod(e.target.value)}>
                <option>Hoje</option>
                <option>Semana</option>
                <option>Mês</option>
              </select>
              <select value={filters.technician} onChange={(e) => setFilters((f) => ({ ...f, technician: e.target.value }))}>
                <option>Todos</option>
                {technicians.map((t) => <option key={t}>{t}</option>)}
              </select>
              <select><option>Todos os modelos</option></select>
              <select><option>Todos os motivos de pausa</option></select>
              <select><option>Todos os motivos de refugo</option></select>
            </div>
            <div className="dashboard-grid top-gap">
              <div className="card">
                <div className="section-title">Previsto x realizado</div>
                <div className="stack-gap top-gap-sm">
                  {filteredOps.map((op) => (
                    <div key={op.id}>
                      <div className="bar-label"><span>{op.id}</span><span>Prev. {op.plannedMin} | Real. {calcNet(op)}</span></div>
                      <div className="dual-bars">
                        <div className="bar-bg"><div className="bar-fill light" style={{ width: '100%' }} /></div>
                        <div className="bar-bg"><div className="bar-fill dark" style={{ width: `${Math.min(Math.round((calcNet(op) / Math.max(op.plannedMin, 1)) * 100), 100)}%` }} /></div>
                      </div>
                    </div>
                  ))}
                </div>
              </div>
              <div className="card">
                <div className="section-title">Pareto de pausas</div>
                <div className="stack-gap top-gap-sm">
                  {[...new Map(ops.flatMap((o) => o.pauses).map((p) => [p.reason, ops.flatMap((o) => o.pauses).filter((x) => x.reason === p.reason).reduce((a, b) => a + b.minutes, 0)])).entries()].map(([reason, minutes]) => (
                    <div key={reason}>
                      <div className="bar-label"><span>{reason}</span><span>{minutes} min</span></div>
                      <div className="bar-bg"><div className="bar-fill amber" style={{ width: `${Math.min((minutes / Math.max(kpis.downtime, 1)) * 100, 100)}%` }} /></div>
                    </div>
                  ))}
                  {ops.flatMap((o) => o.pauses).length === 0 && <div className="muted">Nenhuma pausa registrada.</div>}
                </div>
              </div>
              <div className="card">
                <div className="section-title">Produtividade por técnico</div>
                <div className="stack-gap top-gap-sm">
                  {technicians.slice(0, 3).map((tech, idx) => {
                    const techOps = ops.filter((o) => o.technician === tech && o.status.startsWith('Finalizada'));
                    const perf = techOps.length ? Math.round((techOps.reduce((a, o) => a + o.plannedMin, 0) / Math.max(techOps.reduce((a, o) => a + calcNet(o), 0), 1)) * 100) : 0;
                    return (
                      <div key={tech} className="rank-item">
                        <div>
                          <div><strong>{idx + 1}. {tech}</strong></div>
                          <div className="muted small">OPs concluídas: {techOps.length}</div>
                        </div>
                        <div className="rank-value">{perf}%</div>
                      </div>
                    );
                  })}
                </div>
              </div>
              <div className="card">
                <div className="section-title">Refugo por modelo</div>
                <div className="stack-gap top-gap-sm">
                  {ops.map((op) => {
                    const rate = (op.approved + op.rejected) ? ((op.rejected / (op.approved + op.rejected)) * 100).toFixed(1) : '0.0';
                    return (
                      <div key={op.id}>
                        <div className="bar-label"><span>{op.model}</span><span>{rate}%</span></div>
                        <div className="bar-bg"><div className="bar-fill red" style={{ width: `${Math.min(Number(rate), 100)}%` }} /></div></div>
                    );
                  })}
                </div>
              </div>
            </div>
          </div>
        )}

        {currentScreen === 'supervision' && (
          <div>
            {renderHeader('Supervisão', 'Governança operacional e gestão das exceções')}
            <div className="filters-row four">
              <input placeholder="Buscar OP" />
              <select><option>Todos os técnicos</option>{technicians.map((t) => <option key={t}>{t}</option>)}</select>
              <select><option>Todos os status</option></select>
              <button className="btn secondary">Pesquisar</button>
            </div>
            <div className="card table-card">
              <div className="table-wrap">
                <table>
                  <thead>
                    <tr>
                      <th>OP</th>
                      <th>Técnico</th>
                      <th>Status</th>
                      <th>Data planejada</th>
                      <th>Realizado</th>
                      <th>Ações</th>
                    </tr>
                  </thead>
                  <tbody>
                    {ops.map((op) => (
                      <tr key={op.id}>
                        <td><strong>{op.id}</strong></td>
                        <td>{op.technician}</td>
                        <td><span className={statusClass(op.status)}>{op.status}</span></td>
                        <td>{op.scheduledDate}</td>
                        <td>{calcNet(op) ? `${calcNet(op)} min` : '-'}</td>
                        <td>
                          <div className="action-row">
                            <button onClick={() => openOp(op.id, op.status === 'Pausada' ? 'paused' : 'execution')} className="btn secondary small-btn">Editar</button>
                            <button onClick={() => reprogramOp(op.id)} className="btn violet small-btn">Reprogramar</button>
                            <button onClick={() => cancelOp(op.id)} className="btn danger small-btn">Cancelar</button>
                          </div>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        )}
      </main>
    </div>
  );
}
