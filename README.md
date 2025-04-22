
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)

Objetos principais 

trader = IQOptionTrader() resultados = ResultadoManager() estrategia = EstrategiaExecutor(trader, resultados) scheduler = SinalScheduler(estrategia)

Funcoes de comandos 

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE): await update.message.reply_text("Bem-vindo ao Bot de Opções Binárias! Use /ajuda para ver os comandos.")

async def ajuda(update: Update, context: ContextTypes.DEFAULT_TYPE): comandos = ( "/configurar - Configurar corretora\n" "/modo demo|real - Alternar conta\n" "/estrategia - Selecionar estratégia\n" "/sinal - Enviar sinal manual (ex: /sinal EURUSD M5 CALL)\n" "/agendar - Agendar sinal (ex: /agendar EURUSD M5 CALL 14:30)\n" "/meussinais - Ver sinais agendados\n" "/cancelar - Cancelar todos os sinais\n" "/resultado - Ver resultados do dia\n" "/limpar - Limpar resultados" ) await update.message.reply_text(f"Comandos disponíveis:\n{comandos}")

async def configurar(update: Update, context: ContextTypes.DEFAULT_TYPE): if len(context.args) != 2: await update.message.reply_text("Envie assim: /configurar email senha") return email, senha = context.args conectado = trader.login(email, senha) if conectado: await update.message.reply_text("Conectado com sucesso na IQ Option!") else: await update.message.reply_text("Erro ao conectar. Verifique os dados.")

async def modo(update: Update, context: ContextTypes.DEFAULT_TYPE): if not context.args or context.args[0].lower() not in ["demo", "real"]: await update.message.reply_text("Use: /modo demo ou /modo real") return tipo = context.args[0] atual = trader.modo(tipo) await update.message.reply_text(f"Modo alterado para {atual}")

async def estrategia_cmd(update: Update, context: ContextTypes.DEFAULT_TYPE): if not context.args: await update.message.reply_text("Use assim: /estrategia TIPO VALOR [GALES] (Ex: /estrategia gale 10 2)") return tipo = context.args[0].upper() valor = float(context.args[1]) if len(context.args) > 1 else 10 gales = int(context.args[2]) if tipo == "GALE" and len(context.args) > 2 else 2 estrategia.configurar(tipo, valor, gales) await update.message.reply_text(f"Estratégia configurada: {tipo} | Valor: R${valor} | Gales: {gales if tipo == 'GALE' else 'N/A'}")

async def sinal(update: Update, context: ContextTypes.DEFAULT_TYPE): if len(context.args) != 3: await update.message.reply_text("Use assim: /sinal EURUSD M5 CALL") return par, tempo, direcao = context.args estrategia.executar(par, tempo, direcao.upper()) await update.message.reply_text("Entrada executada!")

async def agendar(update: Update, context: ContextTypes.DEFAULT_TYPE): if len(context.args) != 4: await update.message.reply_text("Use assim: /agendar EURUSD M5 CALL 14:30") return par, tempo, direcao, horario = context.args scheduler.adicionar_sinal(par,EURUSD-tempo, direcao, horario) await update.message.reply_text(f"Sinal agendado: {par} EURUSD {tempo}M1_M5_M15{CALL PUT} {horario}

async def meussinais(update: Update, context: ContextTypes.DEFAULT_TYPE): sinais = scheduler.listar_sinais() if not sinais: await update.message.reply_text("Nenhum sinal agendado no momento.") return resposta = "Sinais agendados:\n" for s in sinais: resposta += f"{s['par']} {s['tempo']}M {s['direcao']} às {s['horario']}\n" await update.message.reply_text(resposta)

async def cancelar(update: Update, context: ContextTypes.DEFAULT_TYPE): scheduler.cancelar_todos() await update.message.reply_text("Todos os sinais foram cancelados

Define os comandos no menu do Telegram 

bot_commands = [ BotCommand(PARE"Inicia o bot"), BotCommand("ajuda", "Ver lista de comandos"), BotCommand("configurar", "Configurar corretora"), BotCommand("modo", "Alternar entre conta demo/real"), BotCommand("estrategia", "Selecionar estratégia"), BotCommand("sinal", "Enviar sinal manual"), BotCommand("agendar", "Agendar novo sinal"), BotCommand("meussinais", "Ver sinais agendados"), BotCommand("cancelar", "Cancelar todos os sinais"), BotCommand("resultado", "Ver resultados"), BotCommand("limpar", "Limpar resultados") 

